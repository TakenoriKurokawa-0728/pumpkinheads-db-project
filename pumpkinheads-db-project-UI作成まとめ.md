# pumpkinheads-db-project-UI作成まとめ

## UI構築に必要なツール  

1. **Python3** (プログラミング言語)  
   - **何故Python3** : データサイエンスと自動化の標準言語。
   - → 異なるシステム同士を「ガッチャンコ（結合）」させる能力が世界一高い言語。
   - → L3/L4 のパケットを扱うインフラ操作から、PostgreSQLのSQLを実行。
   - → StreamlitによるブラウザUIの構築までを、一本のコード（プロトコル）でシームレスに繋げる。

2. **Streamlit** (UIフレームワーク)
   - **何故Streamlit** : データエンジニアリングに特化したフロントエンド・ツール。
   - → HTML/CSS/JavaScriptの隠蔽 : UI構築に伴う「だるいデザイン作業」をパージし、PythonコードのみでブラウザUIを生成可能。
   - → リアクティブな操作性 : DBから抽出した「定番曲度」などを、スライダーやボタンで直感的に操作（パース）できる。
   - → 高密度な可視化 : Streamlit独自のコンポーネントにより、複雑なリレーションを持つデータを一目で理解して表示できる。


## DL時のトラブル対応

### 1-1. **事象** : 72MBのDLに4分かかった （パケット遅延が発生）
### 1-2. **対応** : WAN側の問題と結論付け、ユーザ側では未対応（祝日によるアクセス過多）
  
##2. **LOG**
- **ping -c 10 web.setup (LAN側デフォゲ：Aterm) を実行。**
  - → レスポンス：1.825ms 〜 3.798ms （ジッタが極小）で安定。パケロス無し。

- kurokawa_takenori@kurokawatakenorinoiMac ~ % ping -c 10 web.setup

- PING web.setup (192.168.0.1): 56 data bytes
- 64 bytes from 192.168.0.1: icmp_seq=0 ttl=255 time=3.798 ms
- 64 bytes from 192.168.0.1: icmp_seq=1 ttl=255 time=2.344 ms
- 64 bytes from 192.168.0.1: icmp_seq=2 ttl=255 time=1.825 ms
- 64 bytes from 192.168.0.1: icmp_seq=3 ttl=255 time=3.389 ms
- 64 bytes from 192.168.0.1: icmp_seq=4 ttl=255 time=2.034 ms
- 64 bytes from 192.168.0.1: icmp_seq=5 ttl=255 time=2.013 ms
- 64 bytes from 192.168.0.1: icmp_seq=6 ttl=255 time=3.062 ms
- 64 bytes from 192.168.0.1: icmp_seq=7 ttl=255 time=3.470 ms
- 64 bytes from 192.168.0.1: icmp_seq=8 ttl=255 time=3.193 ms
- 64 bytes from 192.168.0.1: icmp_seq=9 ttl=255 time=2.013 ms

- --- web.setup ping statistics ---
- 10 packets transmitted, 10 packets received, 0.0% packet loss
- round-trip min/avg/max/stddev = 1.825/2.714/3.798/0.702 ms

- -------------------------------------------------------------

- **ping -c 10 8.8.8.8(WAN側GoogleDNS) を実行。**
  - → 2度のパケロスを確認。seq=8 にて大幅な遅延（time=485.743 ms）を確認。

- kurokawa_takenori@kurokawatakenorinoiMac ~ % ping -c 10 8.8.8.8

- PING 8.8.8.8 (8.8.8.8): 56 data bytes
- 64 bytes from 8.8.8.8: icmp_seq=0 ttl=118 time=14.782 ms
- Request timeout for icmp_seq 1
- Request timeout for icmp_seq 2
- 64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=13.356 ms
- 64 bytes from 8.8.8.8: icmp_seq=4 ttl=118 time=10.709 ms
- 64 bytes from 8.8.8.8: icmp_seq=5 ttl=118 time=15.081 ms
- 64 bytes from 8.8.8.8: icmp_seq=6 ttl=118 time=12.308 ms
- 64 bytes from 8.8.8.8: icmp_seq=7 ttl=118 time=10.126 ms
- 64 bytes from 8.8.8.8: icmp_seq=8 ttl=118 time=485.743 ms
- 64 bytes from 8.8.8.8: icmp_seq=9 ttl=118 time=11.030 ms

- --- 8.8.8.8 ping statistics ---
- 10 packets transmitted, 8 packets received, 20.0% packet loss
- round-trip min/avg/max/stddev = 10.126/71.642/485.743/156.525 ms

- -------------------------------------------------------------

- **traceroute to 8.8.8.8(WAN側GoogleDNS) を実行。**
  - → ３度のパケロスを確認。

- kurokawa_takenori@kurokawatakenorinoiMac ~ % traceroute -d 8.8.8.8

- traceroute to 8.8.8.8 (8.8.8.8), 64 hops max, 40 byte packets
- 1  web.setup (192.168.0.1)  4.318 ms  2.281 ms  1.640 ms
- 2  r-***-***-***-***.-----.jp (***.***.***.***)   9.192 ms  12.274 ms  7.886 ms
- 3  r-***-***-***-***.-----.jp (***.***.***.***)   9.387 ms  14.319 ms  7.123 ms
- 4  r-***-***-***-***.-----.jp (***.***.***.***)   10.577 ms  8.438 ms  9.468 ms
- 5  * r-***-***-***-***.-----.jp ((***.***.***.***)   12.331 ms  14.405 ms
- 6  ***.***.***.*** (***.***.***.***)   12.145 ms
-    ***.***.***.*** (***.***.***.***)   11.511 ms *
- 7  dns.google (8.8.8.8)  14.719 ms  15.311 ms *

- -------------------------------------------------------------

### 実際のクエリ（DDL）

```python

import streamlit as st
import pandas as pd
import psycopg2

# 1. 物理接続の設定（PostgreSQL 16 / Docker）
def get_connection():
    return psycopg2.connect(
        host="localhost",
        database="your_db_name", # 物理DB名をインジェクション
        user="your_user",
        password="your_password",
        port="5432"
    )

# 2. UI基本構造のマウント
st.set_page_config(page_title="PumpkinHeadsDB - Live Energy", layout="wide")
st.title("🎸 PumpkinHeadsDB: 1.8ms Precision UI")
st.write("--- 10 - 3 = 7: 真実のSuccessを可視化する ---")

# 3. 定番曲度（Frequency Rate）のパース（抽出）
st.subheader("📊 Track Frequency Rate (定番曲度の特定)")

try:
    conn = get_connection()
    # 先ほど作成したView (v_track_frequency) を叩く
    query = "SELECT * FROM v_track_frequency ORDER BY frequency_rate DESC"
    df = pd.read_sql(query, conn)
    
    # Streamlitによる高密度な可視化
    st.dataframe(
        df.style.highlight_max(axis=0, subset=['frequency_rate'], color='#ff4b4b'),
        use_container_width=True
    )

    # 4. インタラクティブな操作（スライダーによるフィルタリング）
    threshold = st.slider("定番曲度の閾値を設定 (Physical Filter)", 0, 100, 50)
    filtered_df = df[df['frequency_rate'] >= threshold]
    st.write(f"閾値 {threshold}% 以上の真実データ: {len(filtered_df)} 件")
    st.table(filtered_df[['track_title', 'album_title', 'frequency_rate']])

except Exception as e:
    st.error(f"不整合(3)が発生しました: {e}")
finally:
    if 'conn' in locals(): conn.close()

# 5. サイドバーに「20年の重み」をインジェクション
st.sidebar.info("Engineering by Takenori Kurokawa")
st.sidebar.text("Hardware: iMac M3")
st.sidebar.text("Source: Victor / Qobuz")

```

