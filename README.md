-- 1.8msで「魂のパケット」を抽出するクエリ
SELECT 
    title, 
    duration, 
    catalog_no, 
    verification_source 
FROM m_helloween_tracks 
WHERE energy_level = 10
-- 武範さんの手元にある「真の品番」と「Qobuzの解像度」を精密にマウント
INSERT INTO m_helloween_tracks 
(title, album_name, catalog_no, duration, sampling_rate, energy_level, bpm, lyric_anchor_url, youtube_url, verification_source) 
VALUES
('Eagle Fly Free', 'Keeper II', 'VICP-XXXX', '00:05:08', '96kHz/24bit', 10, 190, 
 'http://www.darklyrics.com', 
 'https://www.youtube.com', 'Victor & Qobuz'),
('Keeper of the Seven Keys', 'Keeper II', 'VICP-XXXX', '00:13:37', '96kHz/24bit', 10, 140, 
 'http://www.darklyrics.com', 
 'https://www.youtube.com', 'Victor & Qobuz (Hi-Res)')
CREATE INDEX idx_track_title ON m_helloween_tracks (title)
-- 1. DEFAULT句をパージし、厳格な設計へリビルド（修正）
CREATE TABLE m_helloween_tracks (
    track_id SERIAL PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    album_name VARCHAR(100),
    catalog_no VARCHAR(50),      -- DEFAULTを削除（現物を確認して入力せよ！）
    duration INTERVAL NOT NULL,
    sampling_rate VARCHAR(20),   -- DEFAULTを削除（ソースごとに異なるため）
    energy_level INT CHECK (energy_level BETWEEN 1 AND 10),
    bpm INT,
    lyric_anchor_url VARCHAR(255),
    youtube_url VARCHAR(255),
    verification_source VARCHAR(100), -- 'Qobuz' や 'Physical CD' 等
    shangri_la_sync_rate DECIMAL(5,2) DEFAULT 100.00 -- これだけは100%であれ
)
