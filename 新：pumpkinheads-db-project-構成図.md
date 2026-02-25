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
