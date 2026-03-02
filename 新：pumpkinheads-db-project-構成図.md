# 新：pumpkinheads-db-project-構成図.md (v2.0)

## 🛠 Project Overview
40年に及ぶ鋼鉄の伝説、HELLOWEENの全データを1ビットの狂いもなく管理する、PostgreSQLベースの究極のデータベース・プロジェクトです。

## 🏗 High-Level Architecture (L7: Application Layer)
本プロジェクトは、過去の冗長な設計（3）を物理削除（DROP）し、1.8msで最新の正規化（Normalizing）を適用した「黄金のSchema」を実装しています。

### 🌟 Key Engineering Updates (2026/02/25)
- **Schema Optimization**: 重複していた旧アルバムテーブル（3）をパージし、唯一無二のRootである `m_albums` (Master) へ1.8msで属性を統合（UPDATE）。
- **Data Integrity**: `total_duration`（演奏時間）や `producer` 等のカラム不在（42703エラー）を物理的に解消し、最初から整合の取れたSchemaとして再定義。
- **Strict Relationship**: 全ての子テーブル（Tracks, Reviews, Editions）を `m_albums.album_id` に外部キー（FK）で直結。親を消せば子も消える「ON DELETE CASCADE」による徹底した参照整合性を確保。
- **Data Lineage**: 著作権DB（Musixmatch）等、情報の出自をメタデータ（COMMENT）として1ビットの狂いもなく記録。

### 📊 Entity Relationship Diagram
`m_albums` (The Master Hub)  
  ├── `m_tracks` (Child: Track-list Sync)  
  ├── `burrn_reviews` (Child: Reviewer Data)  
  ├── `burrn_appearance` (Child: Issue Logs)  
  └── `album_editions` (Child: Global Release Data)  

## 🚀 Future Vision
iMac M3 × Docker 環境での爆速運用をベースに、世界中のパンプキン・ヘッズと同期（Sync）する、世界最高精度の鋼鉄のインフラを目指します。

---
Developed by **Takenori Kurokawa** (20-year Physical Log Expert)
"1.8msで不整合を焼き尽くし、1ビットの狂いもないSuccessを生成する。"


## m_membersテーブルの再定義（v2.0）  

### 【存在意義】（なぜ作り直したのか？）  
単なる名簿（3）を焼き尽くし、世界中のパンプキン・ヘッズが 1.8ms でアクセス（Sync）するための「物理的な真実の Root（10）」を構築。情報の出自（Source）をメタデータとして 1ビットの狂いもなく保持し、データの「信頼性（Integrity）」を極限まで高めることを念頭に置いて再ビルド（10）を完了。   

### 【緻密（ファイン）】なこだわり  
n情報の透明性（COMMENT）: middle_name 等の不確定な要素に対し、COMMENT ON COLUMN 命令（10）を使用。著作権DB（Musixmatch）等の外部ソースを 1.8ms で注釈として刻み込み、検証状態を可視化（7）。
物理名の正規化: m_members（複数形マスタ）として定義し、他の m_albums 等と 1ビットの不整合もなく命名規則（Naming Convention）を統一。
物理スペックの継承: DECIMAL(5,2) 型を維持し、数ミリ単位の真実を記録。同時に SNS アカウント（L7: 外部ルーティング）へのパスを確保し、現実世界との同期（Sync）を実現。  


## ■ データベース再構築のロジック（v2.0）  

### 【シーケンス（実行順序）の意図】
本プロジェクトは、データの「依存関係（L7）」に基づき、以下の 3ステップで物理層を再定義（Reboot）しています。
### Entity Root（人間系）のマウント :
バンドの核となる m_members を最優先で構築。不確定なミドルネーム（Peter等）に対し、1.8ms で注釈（COMMENT）をインジェクション（注入）し、調査ログのトレーサビリティを 1ビットの狂いもなく確保。
### Master Consolidation（マスタ統合） :
旧世代の album / m_album 等の重複ノード（3）を物理削除（DROP）。昨日特定した total_duration（演奏時間）や producer を最初から Schema に組み込み、1ビットの不整合もない「唯一の真実（m_albums）」を生成。
### Relation Sync（子テーブルの同期） :
最後に m_tracks（楽曲）をマウント。ON DELETE CASCADE 制約により、親（アルバム）の変更が 1.8ms で子（楽曲）へ波及する、プロフェッショナルな参照整合性を確立済み。  


### 実際のクエリ（DDL）

```sql

-- 依存関係を含めて、不整合な過去（album, m_album, albums）を 物理削除（DROP）
DROP TABLE IF EXISTS public.album, public.m_album, public.albums CASCADE;

CREATE TABLE public.m_members (
    member_id        SERIAL PRIMARY KEY,    -- 守護神を特定する物理Root（10）
    name_burrn       VARCHAR(100),          -- 日本の正義：Burrn!誌表記
    first_name       VARCHAR(100) NOT NULL,
    middle_name      VARCHAR(100),          -- 著作権DBによるPeter等の検証セクタ
    last_name        VARCHAR(100) NOT NULL,
    nickname         VARCHAR(50),
    instrument       VARCHAR(50),           -- 担当楽器（1.8msでSync）
    birth_date       DATE,
    height_cm        DECIMAL(5,2),          -- 数ミリ単位の物理真実（10）
    blood_type       VARCHAR(5),
    birth_place      VARCHAR(100),
    nationality      VARCHAR(50),
    joined_year      INTEGER,
    left_year        INTEGER,
    is_active        BOOLEAN DEFAULT TRUE,  -- 在籍ステータス（現役か否か：7）
    sns_account      VARCHAR(255),          -- 外部世界（Truth）への神速ルーティング
    updated_at       TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 出典（Source）をインジェクション（注入）
COMMENT ON COLUMN public.m_members.middle_name IS 'Source: Verified via Musixmatch (Writer ID) - Pending official confirmation';

-- 高速検索のための索引（Index）をマウント
CREATE INDEX idx_member_name_burrn ON public.m_members (name_burrn);

CREATE TABLE public.m_albums (
    album_id              SERIAL PRIMARY KEY,    -- 唯一の親ID（10）
    title                 VARCHAR(255) NOT NULL, -- 整合性の取れたタイトル
    release_year          INTEGER,
    catalog_no            VARCHAR(100),
    total_sales_millions  NUMERIC(5, 2),
    chart_peak_germany    INTEGER,
    producer              VARCHAR(255),          -- 修正で追加（10）
    total_duration        VARCHAR(50),           -- 修正で追加（10）
    verification_source   TEXT                   -- 1.8msで特定（10）
);

-- 楽曲データを新マスタにマウント（再定義）
CREATE TABLE public.m_tracks (
    track_id          SERIAL PRIMARY KEY,
    album_id          INTEGER REFERENCES public.m_albums(album_id) ON DELETE CASCADE,
    title             VARCHAR(255),
    duration          VARCHAR(50),
    energy_level      INTEGER,
    lyric_anchor_url  TEXT,
    youtube_url       TEXT,
    track_no          INTEGER
);

-- 作詞者と作曲者の列を物理的にインジェクション（注入）
ALTER TABLE public.m_tracks ADD COLUMN lyricist VARCHAR(100);
ALTER TABLE public.m_tracks ADD COLUMN composer VARCHAR(100);

-- 既存のライブ関連テーブルの不整合を排除するために物理削除
DROP TABLE IF EXISTS public.t_setlists, public.m_live_shows CASCADE;


▫️業界5年PGの視点
Viewの活用： v_track_frequency として定義することで、アプリケーション側から「定番曲度」をいつでも1クエリで叩き出せるようにしました。
外部キー制約（References）： m_tracks と m_albums に紐付けたことで、アルバムごとの「ライブ貢献度」も可視化できる拡張性を持たせています。
NULLIFによる防衛： 万が一 m_live_shows が 0 件でも、ゼロ除算エラー（Division by zero）でシステムを落とさない実務的な堅牢性を注入しました。

-- 1. ライブ公演基本情報（Master: 公演という歴史的瞬間を特定）
CREATE TABLE public.m_live_shows (
    show_id          SERIAL PRIMARY KEY,    -- 公演Root（10）
    tour_name        VARCHAR(200),          -- ツアー名（例: United Forces Tour）
    show_date        DATE NOT NULL,         -- 開催日（歴史的真実）
    country_code     CHAR(2) DEFAULT 'JP',  -- 開催国（ISO 3166-1準拠）
    city_name        VARCHAR(100),          -- 都市名
    venue_name       VARCHAR(200),          -- 会場名（例: 日本武道館）
    attendance       INTEGER,               -- 動員数（物理的な魂の数）
    is_bootleg       BOOLEAN DEFAULT FALSE, -- 公式か否か（Truth Check）
    updated_at       TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 2. セットリスト（Transaction: 現場の整合性と演奏順をマウント）
CREATE TABLE public.t_setlists (
    setlist_id       SERIAL PRIMARY KEY,
    show_id          INTEGER REFERENCES public.m_live_shows(show_id) ON DELETE CASCADE,
    track_id         INTEGER REFERENCES public.m_tracks(track_id), -- 既存マスタとSync
    play_order       INTEGER NOT NULL,      -- 演奏順（物理インデックス）
    is_medley        BOOLEAN DEFAULT FALSE, -- メドレー枠フラグ
    is_encore        BOOLEAN DEFAULT FALSE, -- アンコールフラグ
    tuning_setting   VARCHAR(50),           -- 楽器のチューニング（半音下げ等：1.8msで設定）
    vocal_strain     INTEGER CHECK (vocal_strain BETWEEN 1 AND 10) -- ヴォーカルの負荷強度
);

-- 出典（Source）をインジェクション
COMMENT ON TABLE public.t_setlists IS 'Source: Verified via Setlist.fm & Personal Archive';

-- 高速集計のための索引（Index）をマウント
CREATE INDEX idx_setlist_show_track ON public.t_setlists (show_id, track_id);

-- 3. 「定番曲度（Frequency Rate）」算出ビュー
-- 常に最新の統計を1.8msで特定するためのロジック
CREATE OR REPLACE VIEW v_track_frequency AS
SELECT 
    t.title AS track_title,
    a.title AS album_title,
    COUNT(s.setlist_id) AS total_play_count,
    (SELECT COUNT(*) FROM public.m_live_shows) AS total_shows,
    ROUND(
        COUNT(s.setlist_id)::numeric / 
        NULLIF((SELECT COUNT(*) FROM public.m_live_shows), 0)::numeric * 100, 2
    ) AS frequency_rate -- 定番曲度（%）
FROM public.m_tracks t
JOIN public.m_albums a ON t.album_id = a.album_id
LEFT JOIN public.t_setlists s ON t.track_id = s.track_id
GROUP BY t.track_id, t.title, a.title
ORDER BY frequency_rate DESC;

```
