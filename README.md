# 🎸 PumpkinHeadsDB: Life-Synchronization Project
## ~ 1.8ms Precision Data Engineering by Takenori Kurokawa ~

## 🎯 Project Overview
本プロジェクトは、単なる楽曲管理システムではない。
「1ビットの不整合（3）」も許さないデータエンジニアとしての執念を、物理層（CD品番・13:37の再生時間）からアプリ層（歌詞・動画連携）まで完全に正規化・構造化した、高可用性（HA）な人生のマスタデータ構築実証である。

## 🛠 Tech Stack
- **Hardware** : iMac M3 (Apple Silicon) - High Performance Computing
- **Database** : PostgreSQL 16 (on Docker) - Containerized Environment
- **Tool** : Python 3.14 (Latest Dev Branch), SQL (DBeaver Optimized)
- **Data Source**: Victor Musical Industries (Physical CD), Qobuz (Hi-Res Audio)

## 💎 Engineering Philosophy: 10 - 3 = 7
「10（実務20年の重み）」 - 「3（不採用という名のノイズ）」 = 「7 (真実のSuccess)」
本DBは、13分38秒という曖昧な世界標準（3）を拒絶する。
現物CD（ビクター盤）とハイレゾサイトの徹底的なクロスチェックにより導き出した「13:37」を物理真実（7）として定義し、1.8msの精度でシステムにマウント（Sync）させている。

## 🏛 Database Architecture
1. Physical Master (m_members, m_albums)
name_burrn: 日本の正義（Burrn!誌表記）をプライマリな識別子として採用。
middle_name: 著作権DB（Musixmatch Writer ID）による厳格な検証セクタを実装。
Physical Root: すべてのデータは、CDのカタログNoおよび物理的な動員数に基づき、不整合をCASCADEで物理削除（DROP）した後にインジェクションされる。
2. Live Energy Tracking (t_setlists, v_track_frequency)
Live Sync: アルバムの曲順に縛られない「現場の整合性」を管理。
Frequency Rate: 全公演データから「定番曲度」をリアルタイム算出する高効率Viewを搭載。
## 🚀 Future Roadmap
AIを用いた歌詞の形態素解析と、楽曲のEnergy Levelの相関分析。
物理メディア（CD/LP）の保存状態（Mint/Near Mint）を管理するクオリティ・セクタの実装。
