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
