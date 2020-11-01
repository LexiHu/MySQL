## 建表
```
CREATE TABLE IF NOT EXISTS `tags`(
   `userId` INT UNSIGNED,
   `movieId` INT UNSIGNED,
   `tag` MEDIUMTEXT,
   `timestamp` VARCHAR(100)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `ratings`(
   `userId` INT UNSIGNED,
   `movieId` INT UNSIGNED,
   `rating` FLOAT ,
   `timestamp` VARCHAR(100)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `movies`(
   `movieId` INT UNSIGNED,
   `title` VARCHAR(100),
   `genres` VARCHAR(200)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `links`(
   `movieId` INT UNSIGNED,
   `imdbId` INT UNSIGNED,
   `tmdbId` INT UNSIGNED
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `genome_score`(
   `movieId`  INT UNSIGNED,
   `tagId`  INT UNSIGNED,
   `relevance` FLOAT
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE IF NOT EXISTS `genome_tags`(
   `tagId` INT UNSIGNED,
   `tag` VARCHAR(100)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## -- 导入
```
-- SELECT @@global.secure_file_priv;
LOAD DATA INFILE 'D:\Download\\ml-25m\\ml-25m\\tags.csv' ignore INTO TABLE tags FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES;
LOAD DATA INFILE 'D:\Download\\ml-25m\\ml-25m\\ratings.csv' ignore INTO TABLE ratings FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES;
LOAD DATA INFILE 'D:\Download\\ml-25m\\ml-25m\\movies.csv' ignore INTO TABLE movies FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES;
LOAD DATA INFILE 'D:\Download\\ml-25m\\ml-25m\\links.csv' ignore INTO TABLE links FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES;
LOAD DATA INFILE 'D:\Download\\ml-25m\\ml-25m\\genome-scores.csv' ignore INTO TABLE genome_score FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES;
LOAD DATA INFILE 'D:\Download\\ml-25m\\ml-25m\\genome-tags.csv' ignore INTO TABLE genome_tags FIELDS TERMINATED BY ',' ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES;
```

## -- 创建索引
```
CREATE INDEX tags_index ON tags (userId);
CREATE INDEX tags_tag ON tags (tag);
CREATE INDEX ratings_movieId ON ratings (movieId);
CREATE INDEX ratings_timestamp ON ratings (timestamp);
CREATE INDEX ratings_rating ON ratings (rating);
```
## -- 查询

```
-- 一共有多少不同的用户
SELECT COUNT(DISTINCT userId) FROM tags;

-- 一共有多少不同的电影
SELECT COUNT(DISTINCT movieId) FROM tags;

-- 一共有多少不同的电影种类
SELECT COUNT(DISTINCT tag) FROM tags;

-- 一共有多少电影没有外部链接
SELECT COUNT(*) FROM links WHERE 'imdbld'='' AND 'tmbdld'='';

-- 2018年一共有多少人进行过电影评分
SELECT COUNT(DISTINCT userId) FROM ratings WHERE YEAR(FROM_UNIXTIME(ratings.timestamp))=2018;

-- 2018年评分5分以上的电影及其对应的标签
SELECT  ratings.movieId,tags.tag FROM ratings,tags WHERE YEAR(FROM_UNIXTIME(ratings.timestamp))=2018 AND ratings.rating>=5 AND ratings.movieId=tags.movieId;
```
