# インターネットTV

インターネットTVサービスの作成を通して、DB/SQLを学びます。

データベース設計をした上で、データ取得する SQL を作成します。

以下、作成手順です。

### STEP1
---
#### テーブル設計
テーブルごとにテーブル名、カラム名、データ型、NULL(NULL OK の場合のみ YES と記載)、キー（キーが存在する場合、PRIMARY/INDEX のどちらかを記載）、初期値（ある場合のみ記載）、AUTO INCREMENT（ある場合のみ YES と記載）を記載してください。また、外部キー制約、ユニークキー制約に関しても記載してください。


##### テーブル:genres
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| id        | bigint(10)|           |           | PRIMARY   | YES       | 
| genre_name| varchar(10)|          |           |           |           |


##### テーブル:channels
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| id        | bigint(10)|           |           | PRIMARY   | YES       | 
| channel_name| varchar(10)|        |           |           |           |


##### テーブル:programs
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| genre_id  | bigint(10)|           | PRIMARY   |           |           | 
| channel_id| bigint(10)|           | PRIMARY   |           |           |
| id        | bigint(10)|           | PRIMARY   |           | YES       |
| title     | varchar(50)|          |           |           |           |
| program_detail|       |           |           |           |           |

**外部キー制約**
- genre_idに対して、genresテーブルのidカラムから設定
- channel_idに対して、channelsテーブルのidカラムから設定


##### テーブル:seasons
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| program_id| bigint(10)|           | PRIMARY   |           |           | 
| id        | bigint(10)|           | PRIMARY   |           | YES       |
| season_name| varchar(50)|         |           |           |           |
| season_no | bigint(10)| YES       |           |           |           |

**外部キー制約**
- program_idに対して、programsテーブルのidカラムから設定


##### テーブル:episodes
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| program_id| bigint(10)|           | PRIMARY   |           |           |
| season_id | bigint(10)|           | PRIMARY   |           |           |
| id        | bigint(10)|           | PRIMARY   |           | YES       |
| episode_no| bigint(10)|           |           |           |           |
| episode_name| varchar(50)|        |           |           |           |
| episode_detail| varchar(500)|     |           |           |           |
| video_duration| time  |           |           |           |           |
| release_date| date    |           |           |           |           |

**外部キー制約**
- program_idに対して、programsテーブルのidカラムから設定
- season_idに対して、seasonsテーブルのidカラムから設定


##### テーブル:time_slots
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| channel_id| bigint(10)|           | PRIMARY   |           |           |
| program_id| bigint(10)|           | PRIMARY   |           |           |
| season_id | bigint(10)|           | PRIMARY   |           |           |
| episode_id| bigint(10)|           | PRIMARY   |           |           |
| id        | bigint(10)|           |           |           | YES       |
| start_time| datetime  |           |           |           |           |
| end_time  | datetime  |           |           |           |           |

**外部キー制約**
- channel_idに対して、channelsテーブルのidカラムから設定
- program_idに対して、programsテーブルのidカラムから設定
- season_idに対して、seasonsテーブルのidカラムから設定
- episode_idに対して、episodesテーブルのidカラムから設定


##### テーブル:viewerships
| カラム名 | データ型 | NULL | キー | 初期値 | AUTO INCREMENT | 
|-----------|-----------|-----------|-----------|-----------|-----------|
| program_id| bigint(10)|           | PRIMARY   |           |           |
| season_id | bigint(10)|           | PRIMARY   |           |           |
| episode_id| bigint(10)|           | PRIMARY   |           |           |
| time_slot_id| bigint(10)|         | PRIMARY   |           |           |
| viewership| bigint(10)|           |           | 0         |           |

**外部キー制約**
- program_idに対して、programsテーブルのidカラムから設定
- season_idに対して、seasonsテーブルのidカラムから設定
- episode_idに対して、episodesテーブルのidカラムから設定
- time_slot_idに対して、time_slotsテーブルのidカラムから設定


### STEP2
---
#### データベース構築・テーブル作成


##### データベース構築
```sql
CREATE DATABASE IF NOT EXISTS your_database_name;
-- your_database_name は新しいデータベースの名前です。
-- IF NOT EXISTS を使用することで、同じ名前のデータベースが既に存在している場合にエラーを回避できます。
```

##### genersテーブル作成
```sql
CREATE TABLE genres (
    id bigint(10) PRIMARY KEY AUTO_INCREMENT,
    genre_name varchar(10)
);
```

##### channelsテーブル作成
```sql
CREATE TABLE channels (
    id bigint(10) PRIMARY KEY AUTO_INCREMENT,
    channel_name varchar(10)
);
```


##### programsテーブル作成
```sql
CREATE TABLE programs (
    genre_id bigint(10),
    channel_id bigint(10),
    id bigint(10) PRIMARY KEY AUTO_INCREMENT,
    title varchar(50),
    program_detail varchar(500),
    FOREIGN KEY (genre_id) REFERENCES genres(id),
    FOREIGN KEY (channel_id) REFERENCES channels(id)
);
```


##### seasonsテーブル作成
```sql
CREATE TABLE seasons (
    program_id bigint(10),
    id bigint(10) PRIMARY KEY AUTO_INCREMENT,
    season_name varchar(50),
    season_no bigint(10) DEFAULT NULL,
    FOREIGN KEY (program_id) REFERENCES programs(id)
);
```


##### episodesテーブル作成
```sql
CREATE TABLE episodes (
    program_id bigint(10),
    season_id bigint(10),
    id bigint(10) PRIMARY KEY AUTO_INCREMENT,
    episode_no bigint(10),
    episode_name varchar(50),
    episode_detail varchar(500),
    video_duration time,
    release_date date,
    FOREIGN KEY (program_id) REFERENCES programs(id),
    FOREIGN KEY (season_id) REFERENCES seasons(id)
);
```


##### time_slotsテーブル作成
```sql
CREATE TABLE time_slots (
    channel_id bigint(10),
    program_id bigint(10),
    season_id bigint(10),
    episode_id bigint(10),
    id bigint(10) PRIMARY KEY AUTO_INCREMENT,
    start_time datetime,
    end_time datetime,
    FOREIGN KEY (channel_id) REFERENCES channels(id),
    FOREIGN KEY (program_id) REFERENCES programs(id),
    FOREIGN KEY (season_id) REFERENCES seasons(id),
    FOREIGN KEY (episode_id) REFERENCES episodes(id)
);
```


##### viewershipsテーブル作成
```sql
CREATE TABLE viewerships (
    program_id bigint(10),
    season_id bigint(10),
    episode_id bigint(10),
    time_slot_id bigint(10),
    viewership bigint(10) DEFAULT 0,
    PRIMARY KEY (program_id, season_id, episode_id, time_slot_id),
    FOREIGN KEY (time_slot_id) REFERENCES time_slots(id),
    FOREIGN KEY (program_id) REFERENCES programs(id),
    FOREIGN KEY (season_id) REFERENCES seasons(id),
    FOREIGN KEY (episode_id) REFERENCES episodes(id)
);
```


##### サンプルデータ挿入
```sql
-- genresテーブルにサンプルデータを挿入
INSERT INTO genres (id, genre_name) VALUES
(1, 'アニメ'),
(2, '映画'),
(3, 'ドラマ'),
(4, 'ニュース');

-- channelsテーブルにサンプルデータを挿入
INSERT INTO channels (id, channel_name) VALUES
-- アニメ
(1, 'Anime A'),
(2, 'Anime B'),

-- 映画
(3, 'Movie X'),
(4, 'Movie Y'),

-- ドラマ
(5, 'Drama I'),
(6, 'Drama II'),

-- ニュース
(7, 'News P'),
(8, 'News Q');

-- programsテーブルにサンプルデータを挿入
INSERT INTO programs (genre_id, channel_id, id, title, program_detail) VALUES
-- アニメ
(1, 1, 1, 'Anime Show 1', 'Details of Anime Show 1'),
(1, 2, 2, 'Anime Show 2', 'Details of Anime Show 2'),

-- 映画
(2, 3, 3, 'Movie Program X', 'Details of Movie Program X'),
(2, 4, 4, 'Movie Program Y', 'Details of Movie Program Y'),

-- ドラマ
(3, 5, 5, 'Drama Series I', 'Details of Drama Series I'),
(3, 6, 6, 'Drama Series II', 'Details of Drama Series II'),

-- ニュース
(4, 7, 7, 'News Hourly Update P', 'Details of News Hourly Update P'),
(4, 8, 8, 'News Daily Recap Q', 'Details of News Daily Recap Q');

-- seasonsテーブルにサンプルデータを挿入
INSERT INTO seasons (program_id, id, season_name, season_no) VALUES
-- Anime Show 1 (2 seasons)
(1, 1, 'Season 1', 1),
(1, 2, 'Season 2', 2),

-- Anime Show 2 (3 seasons)
(2, 3, 'Season 1', 1),
(2, 4, 'Season 2', 2),
(2, 5, 'Season 3', 3),

-- Movie Program X (1 season)
(3, 6, 'Season 1', NULL),

-- Movie Program Y (2 seasons)
(4, 7, 'Season 1', 1),
(4, 8, 'Season 2', 2),

-- Drama Series I (3 seasons)
(5, 9, 'Season 1', 1),
(5, 10, 'Season 2', 2),
(5, 11, 'Season 3', 3),

-- Drama Series II (2 seasons)
(6, 12, 'Season 1', 1),
(6, 13, 'Season 2', 2),

-- News Hourly Update P (1 season)
(7, 14, 'Season 1', NULL),

-- News Daily Recap Q (3 seasons)
(8, 15, 'Season 1', 1),
(8, 16, 'Season 2', 2),
(8, 17, 'Season 3', 3);

-- episodesテーブルにサンプルデータを挿入
INSERT INTO episodes (program_id, season_id, id, episode_no, episode_name, episode_detail, video_duration, release_date) VALUES
-- Anime Show 1 - Season 1
(1, 1, 1, 1, 'Episode 1', 'Details of Episode 1', '00:15:00', '2024-01-07'),
(1, 1, 2, 2, 'Episode 2', 'Details of Episode 2', '00:15:00', '2024-01-14'),

-- Anime Show 1 - Season 2
(1, 2, 3, 1, 'Episode 1', 'Details of Episode 1', '00:15:00', '2024-01-21'),
(1, 2, 4, 2, 'Episode 2', 'Details of Episode 2', '00:15:00', '2024-01-28'),

-- Anime Show 2 - Season 1
(2, 3, 5, 1, 'Episode 1', 'Details of Episode 1', '00:20:00', '2024-02-04'),
(2, 3, 6, 2, 'Episode 2', 'Details of Episode 2', '00:20:00', '2024-02-11'),

-- Anime Show 2 - Season 2
(2, 4, 7, 1, 'Episode 1', 'Details of Episode 1', '00:20:00', '2024-02-18'),
(2, 4, 8, 2, 'Episode 2', 'Details of Episode 2', '00:20:00', '2024-02-25'),

-- Anime Show 2 - Season 3
(2, 5, 9, 1, 'Episode 1', 'Details of Episode 1', '00:20:00', '2024-03-03'),
(2, 5, 10, 2, 'Episode 2', 'Details of Episode 2', '00:20:00', '2024-03-10'),

-- Movie Program Y - Season 1
(4, 7, 11, 1, 'Episode 1', 'Details of Episode 1', '01:45:00', '2024-03-17'),
(4, 7, 12, 2, 'Episode 2', 'Details of Episode 2', '01:45:00', '2024-03-24'),

-- Movie Program Y - Season 2
(4, 8, 13, 1, 'Episode 1', 'Details of Episode 1', '01:45:00', '2024-03-31'),
(4, 8, 14, 2, 'Episode 2', 'Details of Episode 2', '01:45:00', '2024-04-07'),

-- Drama Series I - Season 1
(5, 9, 15, 1, 'Episode 1', 'Details of Episode 1', '00:25:00', '2024-04-14'),
(5, 9, 16, 2, 'Episode 2', 'Details of Episode 2', '00:25:00', '2024-04-21'),

-- Drama Series I - Season 2
(5, 10, 17, 1, 'Episode 1', 'Details of Episode 1', '00:25:00', '2024-04-28'),
(5, 10, 18, 2, 'Episode 2', 'Details of Episode 2', '00:25:00', '2024-05-05'),

-- Drama Series I - Season 3
(5, 11, 19, 1, 'Episode 1', 'Details of Episode 1', '00:25:00', '2024-05-12'),
(5, 11, 20, 2, 'Episode 2', 'Details of Episode 2', '00:25:00', '2024-05-19'),

-- Drama Series II - Season 1
(6, 12, 21, 1, 'Episode 1', 'Details of Episode 1', '00:30:00', '2024-05-26'),
(6, 12, 22, 2, 'Episode 2', 'Details of Episode 2', '00:30:00', '2024-06-02'),

-- Drama Series II - Season 2
(6, 13, 23, 1, 'Episode 1', 'Details of Episode 1', '00:30:00', '2024-06-09'),
(6, 13, 24, 2, 'Episode 2', 'Details of Episode 2', '00:30:00', '2024-06-16'),

-- News Daily Recap Q - Season 1
(8, 15, 25, 1, 'Episode 1', 'Details of Episode 1', '00:10:00', '2024-06-23'),
(8, 15, 26, 2, 'Episode 2', 'Details of Episode 2', '00:10:00', '2024-06-30'),

-- News Daily Recap Q - Season 2
(8, 16, 27, 1, 'Episode 1', 'Details of Episode 1', '00:10:00', '2024-07-07'),
(8, 16, 28, 2, 'Episode 2', 'Details of Episode 2', '00:10:00', '2024-07-14'),

-- News Daily Recap Q - Season 3
(8, 17, 29, 1, 'Episode 1', 'Details of Episode 1', '00:10:00', '2024-07-21'),
(8, 17, 30, 2, 'Episode 2', 'Details of Episode 2', '00:10:00', '2024-07-28');

-- time_slotsテーブルにサンプルデータを挿入
INSERT INTO time_slots (channel_id, program_id, season_id, episode_id, start_time, end_time)
VALUES
-- Anime Show 1 - Season 1
(1, 1, 1, 1, '2024-01-07 12:00:00', '2024-01-07 12:15:00'),
(1, 1, 1, 2, '2024-01-14 12:00:00', '2024-01-14 12:15:00'),

-- Anime Show 1 - Season 2
(1, 1, 2, 3, '2024-01-21 12:00:00', '2024-01-21 12:15:00'),
(1, 1, 2, 4, '2024-01-28 12:00:00', '2024-01-28 12:15:00'),

-- Anime Show 2 - Season 1
(2, 2, 3, 5, '2024-02-04 15:30:00', '2024-02-04 15:50:00'),
(2, 2, 3, 6, '2024-02-11 15:30:00', '2024-02-11 15:50:00'),

-- Anime Show 2 - Season 2
(2, 2, 4, 7, '2024-02-18 15:30:00', '2024-02-18 15:50:00'),
(2, 2, 4, 8, '2024-02-25 15:30:00', '2024-02-25 15:50:00'),

-- Anime Show 2 - Season 3
(2, 2, 5, 9, '2024-03-03 15:30:00', '2024-03-03 15:50:00'),
(2, 2, 5, 10, '2024-03-10 15:30:00', '2024-03-10 15:50:00'),

-- Movie Program Y - Season 1
(4, 4, 7, 11, '2024-03-24 20:00:00', '2024-03-24 21:45:00'),
(4, 4, 7, 12, '2024-03-31 20:00:00', '2024-03-31 21:45:00'),

-- Movie Program Y - Season 2
(4, 4, 8, 13, '2024-04-07 20:00:00', '2024-04-07 21:45:00'),
(4, 4, 8, 14, '2024-04-14 20:00:00', '2024-04-14 21:45:00'),

-- Drama Series I - Season 1
(5, 5, 9, 15, '2024-04-21 14:30:00', '2024-04-21 14:55:00'),
(5, 5, 9, 16, '2024-04-28 14:30:00', '2024-04-28 14:55:00'),

-- Drama Series I - Season 2
(5, 5, 10, 17, '2024-05-05 14:30:00', '2024-05-05 14:55:00'),
(5, 5, 10, 18, '2024-05-12 14:30:00', '2024-05-12 14:55:00'),

-- Drama Series I - Season 3
(5, 5, 11, 19, '2024-05-19 14:30:00', '2024-05-19 14:55:00'),
(5, 5, 11, 20, '2024-05-26 14:30:00', '2024-05-26 14:55:00'),

-- Drama Series II - Season 1
(6, 6, 12, 21, '2024-06-02 17:00:00', '2024-06-02 17:30:00'),
(6, 6, 12, 22, '2024-06-09 17:00:00', '2024-06-09 17:30:00'),

-- Drama Series II - Season 2
(6, 6, 13, 23, '2024-06-16 17:00:00', '2024-06-16 17:30:00'),
(6, 6, 13, 24, '2024-06-23 17:00:00', '2024-06-23 17:30:00'),

-- News Daily Recap Q - Season 1
(8, 8, 15, 25, '2024-06-30 23:45:00', '2024-06-30 23:55:00'),
(8, 8, 15, 26, '2024-07-07 23:45:00', '2024-07-07 23:55:00'),

-- News Daily Recap Q - Season 2
(8, 8, 16, 27, '2024-07-14 23:45:00', '2024-07-14 23:55:00'),
(8, 8, 16, 28, '2024-07-21 23:45:00', '2024-07-21 23:55:00'),

-- News Daily Recap Q - Season 3
(8, 8, 17, 29, '2024-07-28 23:45:00', '2024-07-28 23:55:00'),
(8, 8, 17, 30, '2024-08-04 23:45:00', '2024-08-04 23:55:00');

-- viewershipsテーブルにサンプルデータを挿入
INSERT INTO viewerships (program_id, season_id, episode_id, time_slot_id, viewership) VALUES
(1, 1, 1, 1, 100),
(1, 1, 2, 1, 120),
(1, 2, 3, 5, 150),
(1, 2, 4, 5, 180),
(2, 3, 5, 9, 200),
(2, 3, 6, 9, 220),
(2, 4, 7, 13, 250),
(2, 4, 8, 13, 280),
(2, 5, 9, 17, 300),
(2, 5, 10, 17, 320),
(4, 7, 11, 21, 500),
(4, 7, 12, 21, 550),
(4, 8, 13, 23, 600),
(4, 8, 14, 23, 650),
(5, 9, 15, 15, 800),
(5, 9, 16, 15, 850),
(5, 10, 17, 17, 900),
(5, 10, 18, 17, 950),
(5, 11, 19, 19, 1000),
(5, 11, 20, 19, 1050),
(6, 12, 21, 25, 1200),
(6, 12, 22, 25, 1250),
(6, 13, 23, 27, 1300),
(6, 13, 24, 27, 1350),
(8, 15, 25, 29, 300),
(8, 15, 26, 29, 320),
(8, 16, 27, 31, 340),
(8, 16, 28, 31, 360);
```


### STEP3
---
#### 以下のデータを抽出するクエリを書きます。

##### 1. よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください
```sql
SELECT p.title, s.season_no, e.episode_no, e.episode_name, v.viewership
FROM viewerships AS v
INNER JOIN programs AS p ON v.program_id = p.id
INNER JOIN episodes AS e ON v.episode_id = e.id
INNER JOIN seasons AS s ON e.season_id = s.id
ORDER BY v.viewership DESC
LIMIT 3;
```


##### 2. よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください
```sql
SELECT p.title, s.season_no, e.episode_no, e.episode_name, v.viewership
FROM viewerships AS v
INNER JOIN programs AS p ON v.program_id = p.id
INNER JOIN episodes AS e ON v.episode_id = e.id
INNER JOIN seasons AS s ON e.season_id = s.id
ORDER BY v.viewership DESC
LIMIT 3;
```


##### 3. 本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします
```sql
SELECT c.channel_name, t.start_time, t.end_time, s.season_no, e.episode_no, e.episode_name, e.episode_detail
FROM time_slots AS t
INNER JOIN channels AS c ON t.channel_id = c.id
INNER JOIN seasons AS s ON t.program_id = s.program_id
INNER JOIN episodes AS e ON t.program_id = e.program_id
WHERE t.start_time BETWEEN '2024-01-07 00:00:00' AND '2024-01-07 23:59:59';
```


##### 4. ドラマというチャンネルがあったとして、ドラマのチャンネルの番組表を表示するために、本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください
```sql
SELECT c.channel_name,t.start_time,t.end_time,s.season_no,e.episode_no,e.episode_name,e.episode_detail
FROM time_slots AS t
INNER JOIN channels AS c ON t.channel_id = c.id
INNER JOIN seasons AS s ON t.season_id = s.id
INNER JOIN episodes AS e ON t.episode_id = e.id
WHERE c.channel_name IN ('Drama I', 'Drama II') AND
t.start_time BETWEEN '2024-01-07 00:00:00' AND DATE_ADD('2024-01-07 00:00:00', INTERVAL 7 DAY);

-- 上記のSQL文ではサンプルデータ内に該当するデータがないため、日付を2024-04-21に設定すると該当するデータを抽出できます
```

### 以上です
