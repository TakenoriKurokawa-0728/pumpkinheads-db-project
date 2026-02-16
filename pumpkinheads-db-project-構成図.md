# PumpkinheadsDB：テーブル構築図


## ■ Membersテーブルの作成  

### 【存在意義】（なぜ作ったのか？）  
バンドの核である「人間（エンティティ）」を管理する最重要レイヤー。名前、パート、身体的スペック（身長・血液型）だけでなく、歴史（加入年）まで細分化し、情報としてアナライジングした際の構造度を最大化させることを念頭に置いて作成。   

### 【緻密（ファイン）】なこだわり  
name_burrn : 日本のファンにとっての正解データである「Burrn!誌」の表記を基準（示名条片）とした。  
ミドルネームの独立 : Ingo Joachim を一括りにせず細分化し、将来の柔軟な検索（LIKE句）を可能にした。  
身長の精度 : DECIMAL(5,2) 型を採用し、数ミリ単位の真実を記録可能とした。  

### 実際のクエリ（DDL）

```sql

CREATE TABLE m_members (
    member_id SERIAL PRIMARY KEY,      -- 守護神を 特定する PK
    name_burrn VARCHAR(100),           -- Burrn! 誌の正義
    first_name VARCHAR(100) NOT NULL,
    middle_name VARCHAR(100),
    last_name VARCHAR(100) NOT NULL,
    nickname VARCHAR(50),
    instrument VARCHAR(50),            -- 担当楽器（Vocal, Guitar, etc.）
    birth_date DATE,
    height_cm DECIMAL(5,2),
    blood_type VARCHAR(5),
    birth_place VARCHAR(100),
    nationality VARCHAR(50),
    joined_year INTEGER,
    left_year INTEGER,
    is_active BOOLEAN DEFAULT TRUE,    -- 在籍ステータス（現役か否か：7）
    -- 【重要】SNSアカウント：外部世界（Truth）への神速ルーティング
    sns_account VARCHAR(255)           
);

-- 特定のメンバーを SELECT するための索引
CREATE INDEX idx_member_name ON m_members (name_burrn);

```


## ■ データ投入（INSERT）の記録：United Forces  

### 【インサートのシーケンス（投入順序）とその意義】  
HELLOWEENの名の下に、最強の布陣「United Forces」として集いし7名の守護神を歴史的背景に基づいたシーケンスでスタジオイン（INSERT）させる。  

1. **Michael Weikath** (Guitar / 結成メンバー)  
   - 本DBの最重要人物であり、バンドの核そのもの。

2. **Markus Grosskopf** (Bass / 結成メンバー)  
   - ヴァイキーと共に一度も脱退していないSole Survivor。 

3. **Andi Deris** (Vocals / 1994年加入)  
   - バンドの「声（Interface）」を再定義し、黄金期を支えるフロントマン。  

4. **Sascha Gerstner** (Guitar / 2002年加入)  
   - 20年以上にわたり、ヴァイキーの対旋律（ハモり）を精密に構築してきた。  

5. **Daniel Löble** (Drums / 2005年加入)  
   - 鉄壁のリズムを支える、現代HELLOWEENの心臓部（Clock）。  

6. **Kai Hansen** (Guitar & Vocals / 脱退後、2017年復帰)  
   - オリジナルメンバーにして「守護神伝」の創造主の一人。
   - ジャーマンメタル界のゴッドファーザー。  

7. **Michael Kiske** (Vocals / 脱退後、2017年復帰)   
   - リユニオン（再統合）の象徴。レジェンド級のヴォーカリスト。
   - カイと共にバンドが、「真の整合性」を取り戻した最後のピース。
   - 脱退時の経緯を考えると、再加入自体が奇跡。
   - 当時を知る人間なら、再びヴァイキーと同じ舞台に立つことを誰が想像出来たであろうか。


### 【ミドルネーム欠損データの設計指針（示名条片の定義）】  
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
('Michael Weikath', 'Michael', 'Ingo Joachim', 'Weikath', 'Weiki', 'Guitar', '1962-08-07', 190.00, 'O', 1984, TRUE),
('Markus Grosskopf', 'Markus', '', 'Grosskopf', 'Markus', 'Bass', '1965-09-21', 192.00, 'O', 1984, TRUE),
('Andi Deris', 'Andreas', '', 'Deris', 'Andi', 'Vocals', '1964-08-18', 178.00, 'B', 1994, TRUE),
('Sascha Gerstner', 'Sascha', '', 'Gerstner', 'Sascha', 'Guitar', '1977-04-02', 198.00, 'O', 2002, TRUE),
('Daniel Löble', 'Daniel', '', 'Löble', 'Dani', 'Drums', '1973-02-22', 180.00, 'A', 2005, TRUE),
('Kai Hansen', 'Kai', 'Michael', 'Hansen', 'Kai', 'Guitar & Vocals', '1963-01-17', 175.00, 'A', 1984, TRUE),
('Michael Kiske', 'Michael', '', 'Kiske', 'Michi', 'Vocals', '1968-01-24', 182.00, 'O', 1986, TRUE);

```


## ■ Albumsテーブルの作成  

###  【存在意義】（なぜ作ったのか？）  
メンバーが紡ぎ出した「作品（プロダクト）」を管理する、DB構造の第2階層。  
単なるタイトル管理に留まらず、発売日、プロデューサー、さらには「盤（エディション）」ごとの差異をデータ化することで、音楽史のバージョニング（版管理）を可能にするため。  

## ■ アルバム・エディション管理の再定義  

### 【存在意義】（なぜ分離したのか？）  
同じ『The Dark Ride』でも、日本盤（Victor）とドイツ盤（Nuclear Blast）では品番、発売日、ボーナストラックの有無（データの整合性）が異なる。  
「1つの作品（論理：Logic）」と「世界中の盤（物理：Physical）」を切り分けることで、1ビットの狂いもない世界規模のアーカイブを実現する。  

###  【緻密（ファイン）】なこだわり  
title_burrn : 日本のメタラーの聖典「Burrn!誌」の表記を正として採用。  
release_date : 記念碑的な「発売日」は、年月日まで DATE型 で厳密に刻む。  
is_remastered / catalog_number : 「同じ作品でも音が違う（リマスター）」や「品番が違う（再販）」という不都合な真実を、フラグと品番で精密に識別。  
country_code : ISO 3166-1（2文字） 準拠。'JP', 'DE' 等、世界標準の示名条片（ラベル）で国を特定。  
catalog_number : 日本の VICP-61169 も、ドイツの NB 543-2 も、それぞれ独立したエンティティとして永続化（Commit）。  
burrn_reviews : 『Chameleon』のように評価が分かれる作品を考慮。平野氏（のちの『炎』編集長）が下した「80点台の真実」など、レビューアーごとの個別の審判を多対多のリレーションで保持。  


### 実際のクエリ（DDL）

```sql

-- 1. 作品基本情報（論理レイヤー：普遍の真実）
CREATE TABLE albums (
    album_id SERIAL PRIMARY KEY,
    title_burrn VARCHAR(255) NOT NULL,    -- Burrn!誌での表記
    original_release_year INTEGER,         -- オリジナル発売年
    producer VARCHAR(100)                  -- 音の建築士（普遍の属性）
);

-- 2-1. 世界各国の「盤」情報（物理レイヤー）
CREATE TABLE burrn_appearances (
    appearance_id SERIAL PRIMARY KEY,
    album_id INTEGER REFERENCES albums(album_id),
    issue_date VARCHAR(50)                 -- 例：'1993年6月号'
);

-- 2-2. Burrn! 掲載号・レビュー管理（多対多の歴史を記録）
CREATE TABLE burrn_reviews (
    review_id SERIAL PRIMARY KEY,
    album_id INTEGER REFERENCES albums(album_id),
    reviewer_name VARCHAR(50),             -- レビューアー名
    score INTEGER CHECK (score <= 100),    -- 精密な採点
    review_summary TEXT                    -- 評価の要約
);

-- 3. 世界各国の「盤」情報（物理レイヤー）
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


## ■ Live_Showsテーブルの作成  

###  【存在意義】（なぜ作ったのか？）  
バンドの活動実績（活動ログ）を管理する、DB構造の動的レイヤー。作品（Albums）が「完成したプログラム」なら、ライブは「実行ログ（Execution Log）」。ツアー名、開催地、そしてセットリストを紐付けることで、どの楽曲が「ライブの定番（高可用性）」であるかをデータとして証明するため。  

###  【緻密（ファイン）】なこだわり  
venue_name : 日本武道館など、当時の現場（インフラ）の名称を正確に記録。  
play_order : カイ・ハンセン・メドレーのような、1枠の中に複数の曲がネスト（階層化）される複雑な構造を、演奏順（Index）によって論理的に制御。  
is_encore : 公演のクライマックス（アンコール）という特別なステータスをフラグ化。  

### 実際のクエリ（DDL）  

```sql

-- 1. ライブ公演基本情報（マスター：公演というイベントを特定）
CREATE TABLE live_shows (
    show_id SERIAL PRIMARY KEY,
    tour_name VARCHAR(200),               -- ツアー名（例：United Forces Tour）
    show_date DATE NOT NULL,              -- 開催日（歴史的真実）
    country_code CHAR(2) DEFAULT 'JP',    -- 開催国（ISO 3166-1準拠）
    city_name VARCHAR(100),               -- 都市名
    venue_name VARCHAR(200)               -- 会場名（例：日本武道館）
);

-- 2. セットリスト（中間テーブル：どの曲を何番目に演奏したか）
-- アルバムの曲順とは異なる「現場の整合性」を管理
CREATE TABLE setlists (
    setlist_id SERIAL PRIMARY KEY,
    show_id INTEGER REFERENCES live_shows(show_id), -- どのライブか
    track_id INTEGER,                               -- どの曲か（tracksテーブル未作成時は仮）
    play_order INTEGER NOT NULL,                    -- 演奏順（Index）
    is_medley BOOLEAN DEFAULT FALSE,                -- メドレー枠かどうかのフラグ
    is_encore BOOLEAN DEFAULT FALSE                 -- アンコールフラグ
);

-- ライブでの演奏回数をカウントし、全公演数で割る「定番曲度」のロジック
SELECT 
    t.title,
    COUNT(s.setlist_id) AS play_count,
    ROUND(COUNT(s.setlist_id)::numeric / (SELECT COUNT(*) FROM live_shows) * 100, 2) AS frequency_rate
FROM tracks t
JOIN setlists s ON t.track_id = s.track_id
GROUP BY t.title
ORDER BY frequency_rate DESC;

```


ここから消去ーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーー

## ■ m_helloween_tracksテーブルの作成  

### 【存在意義】（なぜ作ったのか？）  
人生のサウンドトラックを司る、DB構造の「マスター・カーネル（核）」。  
音楽を単なる「趣味（3）」という名のノイズで終わらせず、  
「 物理層（品番・秒数） 」「 論理層（整合性） 」「 感情層（Lyric） 」を完全に正規化。  

### 【精密（ファイン）】なこだわり  
duration（13:37）: QobuzやVictorの現物（VICP品番）により証明された「1秒の狂いもない再生時間」。  
世間の13:38という曖昧なパケット（3）を拒絶する、データの整合性（Integrity）への執念。  
lyric_anchor_url: Dark Lyricsの「#2（イーグル）」のように、  
アルバム全曲という巨大なデータセットから、1.8msで特定の楽曲ポインタ（魂）を射抜くための神速ルーティング。  

sampling_rate: 24-Bit / 96.0 kHz というハイレゾ仕様。  
自身のエンジニアとしての「知覚の解像度（QoS）」を物理的に定義。  

### 実際のクエリ（DDL）  

```sql

/* 
 * [m_helloween_tracks] 
 * 1ビットの不整合も許さない、精密（ファイン）な人生のマスタ
 */
CREATE TABLE m_helloween_tracks (
    track_id SERIAL PRIMARY KEY,
    title VARCHAR(100) NOT NULL,            -- 魂のタイトル（Eagle Fly Free等）
    album_name VARCHAR(100),                -- 所属アルバム
    catalog_no VARCHAR(50),                 -- Victor現物品番（VICP-XXXX：真実の鍵）
    duration INTERVAL NOT NULL,               -- 13:37の執念（精密な物理再生時間）
    sampling_rate VARCHAR(30),              -- 解像度（24-Bit/96kHz：エンジニアの視界）
    energy_level INT CHECK (energy_level BETWEEN 1 AND 10), -- 執念のボルテージ
    lyric_anchor_url VARCHAR(255),           -- Dark Lyricsの#アンカー（ピンポイント接続）
    youtube_url VARCHAR(255),                -- 外部ストリーミングへの動的リンク
    verification_source VARCHAR(100),       -- 'Victor & Qobuz' (真実の出所)
    shangri_la_sync_rate DECIMAL(5,2) DEFAULT 100.00 -- 理想郷との同期率
);

-- 1.8msで魂をSELECTするための索引（加速装置）
CREATE INDEX idx_track_energy ON m_helloween_tracks (energy_level DESC);
CREATE INDEX idx_track_title ON m_helloween_tracks (title);

```


ここまで消去ーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーーー


## ■ データベース構造の再定義（Refactoring）  
既存の m_helloween_tracks（単一テーブル構造）を、「 物理マスタ（アルバム） 」と「 論理データ（楽曲） 」に分離。1ビットの無駄（冗長性）も許さない、第三正規化（3NF）で完遂。

### 【存在意義】（なぜ m_albums と m_tracks に分けたのか？）  
不整合のパージ : 1枚のアルバムに紐付く「品番」や「売上」を全楽曲レコードに重複して持たせる設計を排除。
高可用性（HA）の確保 : アルバムを「親（Parent）」、楽曲を「子（Child）」とするリレーション（1:N）を構築することで、データの更新・保守性を最適化。
ビジネス視点のインジェクション : 「売上枚数（Business Impact）」をアルバム単位で正確にマウントするため。  

### 【緻密（ファイン）】なこだわり  
物理IDの同期（ID:3）: 「Keeper 2」はバンドの歴史において3枚目である。自動採番を拒絶し、album_id = 3 を明示的に指定して物理的な歴史と論理IDを同期。
楽曲の再整列（Track No: 9）: 再販盤のSave Us混入による不整合をパージ。KOT7Kを初版盤の9曲目へと再定義。
外部キー制約（REFERENCES）: 親子の「背番号（ID）」を 1ビットの狂いもなく紐付け、野良パケット（孤児レコード）の発生を未然に防ぐ堅牢なインフラ。  

### 実際のクエリ（DDL）  

```sql

/* 
 * [Helloween DB: Physical & Logical Sync Build] 
 * 1ビットの不整合も許さない、シニア・データエンジニアの成果物
 */

-- 1. 過去の遺産（ノイズ）を CASCADE で一括パージ
DROP TABLE IF EXISTS m_helloween_tracks CASCADE;

-- 2. 物理マスタ：アルバム（親）の構築
CREATE TABLE m_albums (
    album_id INT PRIMARY KEY,               -- 3枚目=ID:3 物理インデックス
    title VARCHAR(100) NOT NULL,
    release_year INT NOT NULL,
    catalog_no VARCHAR(50),                 -- 現物CD(VICP)の証跡
    total_sales_millions DECIMAL(5,2),      -- 売上枚数
    chart_peak_germany INT,                 -- 市場への浸透率
    verification_source VARCHAR(100) DEFAULT 'Recording Industry Association'
);

-- 3. 論理マスタ：楽曲（子）の構築（FK結合）
CREATE TABLE m_tracks (
    track_id SERIAL PRIMARY KEY,
    album_id INT REFERENCES m_albums(album_id), -- 親子結合
    track_no INT,                               -- 魂の並び順（9曲目の真実）
    title VARCHAR(100) NOT NULL,
    duration INTERVAL NOT NULL,                 -- 00:13:37 の整合性
    energy_level INT CHECK (energy_level BETWEEN 1 AND 10)
);

-- 4. インデックス・マウント（1.8ms の爆速検索）
CREATE INDEX idx_track_album_sync ON m_tracks (album_id);

-- 5. 魂のインジェクション（COMMIT）
INSERT INTO m_albums (album_id, title, release_year, catalog_no, total_sales_millions, chart_peak_germany)
VALUES (3, 'Keeper of the Seven Keys Part II', 1988, 'VICP-XXXX', 1.00, 5);

INSERT INTO m_tracks (album_id, track_no, title, duration, energy_level)
VALUES (3, 9, 'Keeper of the Seven Keys', '00:13:37', 10);

/* 
 * [m_tracks: Multimedia Integration] 
 * 歌詞(Dark Lyrics)と動画(YouTube)カラムを追加
 */

-- 1. Eagle Fly Free (Keeper 2 / Track 2) 
UPDATE m_tracks 
SET lyric_anchor_url = 'http://www.darklyrics.com',
    youtube_url = 'https://www.youtube.com'
WHERE title = 'Eagle Fly Free' AND album_id = 3;

-- 2. Keeper of the Seven Keys (Keeper 2 / Track 9) 
UPDATE m_tracks 
SET lyric_anchor_url = 'http://www.darklyrics.com',
    youtube_url = 'https://www.youtube.com'
WHERE title = 'Keeper of the Seven Keys' AND album_id = 3;

```

### UIで累計売上枚数を獲得するコード（後日実装予定）
### 実際のソースコード(album_sales_fetcher.py)  

```Python

import requests  # 外部へパケットを投げるライブラリ
import psycopg2  # iMac M3 内の PostgreSQL とハンドシェイク(Sync)用

def fetch_and_update_sales():
    # 1. DBへのコネクション確立
    conn = psycopg2.connect("dbname=helloween user=takenori")
    cur = conn.cursor()

    # 2. 今現在17枚のアルバムタイトルという名の「 器(latest_sales) 」をSELECT
    cur.execute("SELECT album_id, title FROM m_albums")
    albums = cur.fetchall()

    for album_id, title in albums:
        # 3. 外部API(Discogs等)へ「売上/統計を教えろ」とリクエスト
        # ※本来はAPIキーが必要ですが、ロジックを記述
        api_url = f"https://api.discogs.com{title}&artist=Helloween"
        
        try:
            response = requests.get(api_url, timeout=1.8) # 1.8msの神速タイムアウト設定
            if response.status_code == 200:
                # 取得した最新の売上枚数（パケット）をパース（解析）
                data = response.json()
                # 例：統計データから「所有者数」などを売上の代替指標として抽出
                latest_count = data['results'][0]['community']['have'] / 10000.0 # 単位:M
                
                # 4. iMac M3 の DB を最新の「 累計（Success） 」で UPDATE！
                cur.execute("""
                    UPDATE m_albums 
                    SET total_sales_millions = %s, 
                        verification_source = 'Discogs API Auto-Sync'
                    WHERE album_id = %s
                """, (latest_count, album_id))
                
                print(f"Success: {title} -> {latest_count}M Updated! (200 OK)")
        except Exception as e:
            print(f"Error at {title}: {e} (404/500)")

    conn.commit() # 真実を確定
    cur.close()
    conn.close()

if __name__ == "__main__":
    fetch_and_update_sales()

```


## ■ マガジン・アーカイブ（m_magazines / t_magazine_contents）の構築

### 【存在意義】 
情報の「最小単位（L1）」への細分化と、その「成り立ち（7）」の観測を目的とした、多次元ナレッジ・ベース。
雑誌名という「大きな括り（3：ノイズ）」をパージし、「 インタビュー（言霊） 」「 レビュー（評価） 」「 タブ譜（物理奏法） 」という独立した真実（7）として保存。1.8msで特定の「奏法」や「証言」へアクセス可能にする。  

### 【緻密（ファイン）】なこだわり  
   - 雑誌の基本情報（m_magazines）と、掲載記事の内容（t_magazine_contents）を 1ビットの狂いもなく正規化。これにより『Bassマガジン』等への「 無限のスケーラビリティ（汎用性）」を確保。
   - 物理奏法へのポインタ（has_tab_score）:
単なる「持っている（3）」という情報ではなく、「 実際に弾ける（10）」という物理層の価値を BOOLEAN 型で定義。1.8msで全アーカイブから「タブ譜あり」の号だけを SELECT 可能。
   - 評価の定量化（review_score）:
『Burrn!』等の点数を CHECK (between 0 and 100) 制約で保護。不整合な数値（3）の混入を 1.8ms で未然に防ぐ堅牢な整合性。

### 実際のクエリ（DDL）  

```sql

/* 
 * [Magazine Archive System: Normalized Build] 
 * 1ビットの不整合も許さない、精密（ファイン）な情報の解体と再構築
 */

-- 1. 媒体マスタ：Burrn! や Bass Magazine の器
CREATE TABLE m_magazines (
    magazine_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,            -- 雑誌名
    publisher VARCHAR(100),                -- 版元（シンコーミュージック等）
    country VARCHAR(50) DEFAULT 'Japan'
);

-- 2. 記事内容：インタビュー・レビュー・タブ譜の「 成り立ち 」を記録
CREATE TABLE t_magazine_contents (
    content_id SERIAL PRIMARY KEY,
    magazine_id INT REFERENCES m_magazines(magazine_id), -- 親子結合(FK)
    member_id INT,                                       -- 誰の特集か
    publish_date DATE,                                   -- 発行日
    
    has_interview BOOLEAN DEFAULT FALSE,                 -- 言霊の有無
    has_review BOOLEAN DEFAULT FALSE,                    -- 評価の有無
    has_tab_score BOOLEAN DEFAULT FALSE,                 -- 物理奏法の有無
    
    review_score INT,                                    -- Burrn!点数等
    summary_text TEXT,                                   -- 1.8msでパースするサマリー
    page_number INT,                                     -- 物理ページ位置
    
    CONSTRAINT check_review_score CHECK (review_score BETWEEN 0 AND 100)
);

-- 加速装置：1.8ms で「 タブ譜あり 」を射抜くための索引
CREATE INDEX idx_tab_sync ON t_magazine_contents (has_tab_score) WHERE has_tab_score = TRUE;

```
