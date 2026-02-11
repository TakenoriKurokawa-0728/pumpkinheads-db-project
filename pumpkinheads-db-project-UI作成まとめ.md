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

1. **事象** : 72MBのDLに4分かかった （パケット遅延が発生） 
   - **何故Python3** : データサイエンスと自動化の標準言語。
   - → 異なるシステム同士を「ガッチャンコ（結合）」させる能力が世界一高い言語。
   - → L3/L4 のパケットを扱うインフラ操作から、PostgreSQLのSQLを実行。
   - → StreamlitによるブラウザUIの構築までを、一本のコード（プロトコル）でシームレスに繋げる。
  
2. **LOG**
- **ping web.setup (LAN側デフォゲ：Aterm) を実行。**
- → レスポンス：3.470ms 〜 3.798ms で安定。パケロス無し。

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
