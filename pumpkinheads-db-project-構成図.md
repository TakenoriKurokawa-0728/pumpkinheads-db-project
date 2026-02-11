# PumpkinheadsDB：テーブル構築図


## ■ Membersテーブルの作成  

### 【存在意義】（なぜ作ったのか？）  
バンドの核である「人間（エンティティ）」を管理する最重要レイヤー。  
名前、パート、身体的スペック（身長・血液型）だけでなく、歴史（加入年）まで細分化し、  
情報としてアナライジングした際の構造度を最大化させることを念頭に置いて作成。   

### 【緻密（ファイン）】なこだわり  
name_burrn: 日本のファンにとっての正解データである「Burrn!誌」の表記を基準（示名条片）とした。  
ミドルネームの独立: Ingo Joachim を一括りにせず細分化し、将来の柔軟な検索（LIKE句）を可能にした。  
身長の精度: DECIMAL(5,2) 型を採用し、数ミリ単位の真実（7）を記録可能とした。  

### 実際のクエリ（DDL）

```sql
CREATE TABLE members (
    member_id SERIAL PRIMARY KEY,      -- 守護神が振る唯一無二のID
    name_burrn VARCHAR(100),           -- Burrn!誌での表記
    first_name VARCHAR(100) NOT NULL,  -- 名
    middle_name VARCHAR(100),          -- ミドルネーム
    last_name VARCHAR(100) NOT NULL,   -- 姓
    nickname VARCHAR(50),              -- 愛称
    instrument VARCHAR(50),            -- 担当楽器
    birth_date DATE,                   -- 生年月日
    height_cm DECIMAL(5,2),            -- 身長（精密な数値型）
    blood_type VARCHAR(5),             -- 血液型
    birth_place VARCHAR(100),          -- 出身地
    nationality VARCHAR(50),           -- 国籍
    joined_year INTEGER,               -- 加入年
    left_year INTEGER,                 -- 脱退年
    is_active BOOLEAN DEFAULT TRUE     -- 在籍ステータス
); 
```


## ■ データ投入（INSERT）の記録：United Forces  

【インサートのシーケンス（投入順序）とその意義】  
HELLOWEENの名の下に、最強の布陣「United Forces」として集いし7名の守護神を  
歴史的背景に基づいたシーケンスで入城（INSERT）させる。  

1. **Michael Weikath** (Guitar / 結成メンバー)  
   - **理由**: 本DBの主権者であり、全てのメロディの起点（Root）であるため。 

2. **Markus Grosskopf** (Bass / 結成メンバー)  
   - **理由**: ヴァイキーと共に一度も脱退せず「城」を守り続けてきた、リズムの根幹（Infrastructure）であるため。 

3. **Andi Deris** (Vocals / 1994年加入)  
   - **理由**: バンドの「声（Interface）」を再定義し、黄金期を支えるフロントマン。  

4. **Sascha Gerstner** (Guitar / 2002年加入)  
   - **理由**: 20年以上にわたり、ヴァイキーの対旋律（ハモり）を精密に構築してきた。  

5. **Daniel Löble** (Drums / 2005年加入)  
   - **理由**: 鉄壁のリズムを支える、現代HELLOWEENの心臓部（Clock）。  

6. **Kai Hansen** (Guitar & Vocals / 脱退後、2017年復帰)  
   - **理由**: オリジナルメンバーにして「守護神伝」の創造主にして、ジャーマンメタル界のゴッドファーザー。  

7. **Michael Kiske** (Vocals / 脱退後、2017年復帰)   
   - **理由**: リユニオン（再統合）の象徴。比類なきハイトーン。カイと共に「真の整合性」を取り戻した最後のピース。


【ミドルネーム欠損データの設計指針（示名条片の定義）】
本プロジェクトでは、ミドルネームを持たないメンバーに対し、  
ハイフン（-）や特定の記号を排し、空文字（''）を格納する設計を採用した。  

1. **検索の整合性**
   - **理由** : - 等の記号を代入すると、将来的に LIKE 演算子を用いた「部分一致検索」を行う際、記号自体がノイズとなり、正確なフィルタリングを阻害するため。
2. **出力の柔軟性**
   - **理由** : SQLの CONCAT 等でフルネームを連結表示する際、空文字であれば余計な記号が混入せず、Burrn!誌等に準拠した美しい姓名表示をリアルタイムに生成できるため。
3. **実在の証明**
   - **理由** : 「値が不明」なのではなく「ミドルネームが存在しない」という事実を、最短のパケット（空文字）で定義した。

### 実際のクエリ（DDL）

```sql
INSERT INTO members (name_burrn, first_name, middle_name, last_name, nickname, instrument, birth_date, height_cm, blood_type, joined_year, is_active)
VALUES 
('Markus Grosskopf', 'Markus', '', 'Grosskopf', 'Markus', 'Bass', '1965-09-21', 190.00, 'O', 1984, TRUE),
('Andi Deris', 'Andreas', '', 'Deris', 'Andi', 'Vocals', '1964-08-18', 178.00, 'B', 1994, TRUE),
('Sascha Gerstner', 'Sascha', '', 'Gerstner', 'Sascha', 'Guitar', '1977-04-02', 192.00, 'O', 2002, TRUE),
('Daniel Löble', 'Daniel', '', 'Löble', 'Dani', 'Drums', '1973-02-22', 180.00, 'A', 2005, TRUE),
('Kai Hansen', 'Kai', 'Michael', 'Hansen', 'Kai', 'Guitar & Vocals', '1963-01-17', 175.00, 'A', 1984, TRUE),
('Michael Kiske', 'Michael', '', 'Kiske', 'Michi', 'Vocals', '1968-01-24', 182.00, 'O', 1986, TRUE);
```


## ■ Albumsテーブルの作成  

###【存在意義】（なぜ作ったのか？）  
メンバーが紡ぎ出した「作品（プロダクト）」を管理する、DB構造の第2階層。  
単なるタイトル管理（10）に留まらず、発売日、プロデューサー、  
さらには「盤（エディション）」ごとの差異をデータ化することで、音楽史のバージョニング（版管理）を可能にするため。  

## ■ アルバム・エディション管理の再定義  

### 【存在意義】（なぜ分離したのか？）  
同じ『The Dark Ride』でも、日本盤（Victor）とドイツ盤（Nuclear Blast）では  
品番、発売日、ボーナストラックの有無（データの整合性）が異なる。  
「1つの作品（論理：Logic）」と「世界中の盤（物理：Physical）」を切り分けることで、1ビットの狂いもない世界規模のアーカイブを実現する。  

【精密（ファイン）】なこだわり  
title_burrn: 日本のメタラーの聖典「Burrn!誌」の表記を正（真実：7）として採用。  
release_date: 記念碑的な「発売日」は、年月日まで DATE型 で厳密に刻む。  
is_remastered / catalog_number: 「同じ作品でも音が違う（リマスター）」や「品番が違う（再販）」という不都合な真実を、フラグと品番で精密に識別。  
country_code: ISO 3166-1（2文字） 準拠。'JP', 'DE' 等、世界標準の示名条片（ラベル）で国を特定。  
catalog_number: 日本の VICP-61169 も、ドイツの NB 543-2 も、それぞれ独立したエンティティとして永続化（Commit）。  


### 実際のクエリ（DDL）

```sql
-- 1. 作品基本情報（論理レイヤー）
CREATE TABLE albums (
    album_id SERIAL PRIMARY KEY,
    title_burrn VARCHAR(255) NOT NULL,    -- Burrn!誌での表記
    original_release_year INTEGER          -- オリジナル発売年
);

-- 2. 世界各国の「盤」情報（物理レイヤー）
CREATE TABLE album_editions (
    edition_id SERIAL PRIMARY KEY,
    album_id INTEGER REFERENCES albums(album_id), -- 作品IDで紐付け
    country_code CHAR(2) NOT NULL,                -- 'JP', 'DE' 等
    label VARCHAR(100),                           -- Victor, Nuclear Blast等
    catalog_number VARCHAR(100),                  -- 各国の品番（精密なキー）
    release_date DATE,                            -- 日本先行発売等の差異
    is_remastered BOOLEAN DEFAULT FALSE,          -- リマスターフラグ
    has_bonus_track BOOLEAN DEFAULT FALSE,        -- 日本盤特権の証明
    edition_name VARCHAR(100)                     -- 'Limited Edition'等
);
```
