# PumpkinheadsDB：テーブル構築図

## ■ Membersテーブルの作成  

### 【存在意義】（なぜ作ったのか？）  
バンドの核である「人間（エンティティ）」を管理する最重要レイヤー。  
名前、パート、身体的スペック（身長・血液型）だけでなく、歴史（加入年）まで細分化し、  
情報としてアナライジングした際の構造度を最大化させることを念頭に置いて作成。   

### 【機密（ファイン）】なこだわり  
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

Markus Grosskopf (Bass / 結成メンバー)  
   - **理由**: ヴァイキーと共に一度も脱退せず「城」を守り続けてきた、リズムの根幹（Infrastructure）であるため。 

Andi Deris (Vocals / 1994年加入)  
   - **理由**: バンドの「声（Interface）」を再定義し、黄金期を支えるフロントマン。  

Sascha Gerstner (Guitar / 2002年加入)  
   - **理由**: 20年以上にわたり、ヴァイキーの対旋律（ハモり）を精密に構築してきた。  

Daniel Löble (Drums / 2005年加入)  
   - **理由**: 鉄壁のリズムを支える、現代HELLOWEENの心臓部（Clock）。  

Kai Hansen (Guitar & Vocals / 脱退後、2017年復帰)  
   - **理由**: オリジナルメンバーにして「守護神伝」の創造主にして、ジャーマンメタル界のゴッドファーザー。  

Michael Kiske (Vocals / 脱退後、2017年復帰)   
   - **理由**: リユニオン（再統合）の象徴。比類なきハイトーン。カイと共に「真の整合性」を取り戻した最後のピース。  

### 実際のクエリ（DDL）

```sql

```
