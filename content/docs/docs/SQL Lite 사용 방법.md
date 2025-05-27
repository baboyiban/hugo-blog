---
title: 사용 방법
description: 사용 방법
---

# 초기 설정

## 설치

```shell
brew install sqlite3
```

---

## 실행

```shell
sqlite3 mydb.db
```

---

# SQLite Shell 명령어

## .tables

```sql
.tables person
```

## .schema

```sql
.schema person
```

## .exit

```sql
.exit
```

## .backup

```sql
.backup main b_mydb.db
```

# SQL

## DDL (Data Definition Language: 데이터 정의 언어)

### TABLE

#### CREATE TABLE

```sql
CREATE TABLE person (
	id INTEGER NOT NULL,
	name TEXT NOT NULL,
	age INTEGER,
	PRIMARY KEY (id AUTOINCREMENT)
);
```

#### ALTER TABLE

```sql
ALTER TABLE person ADD COLUMN salary INTEGER NOT NULL;
```

#### DROP TABLE

```sql
DROP TABLE person;
```

### VIEW

#### CREATE VIEW

```sql
CREATE VIEW name_person AS
SELECT name FROM person;
```

#### DROP VIEW

### INDEX

- **인덱스**는 데이터를 빠르게 찾도록 도와주는 목차 같은 구조입니다.
- **인덱스로 검색**하면 전체 데이터를 다 보지 않고 원하는 데이터를 바로 찾아가기 때문에 훨씬 빠릅니다.
- 단, 인덱스는 검색은 빠르게 하지만 삽입/갱신 시 성능에 약간의 영향을 줄 수 있으니 꼭 필요한 컬럼에만 사용하는 게 좋아요.

#### CREATE INDEX

```sql
CREATE INDEX index_person ON person(name);
```

#### DROP INDEX

## DML (Data Manipulation Language: 데이터 조작 언어)

### SELECT

```sql
SELECT * FROM person;
```

### INSERT INTO

```sql
INSERT INTO person (name, age) VALUES ('사원1', 28);
```

### UPDATE

```sql
UPDATE person SET name = '사원2' WHERE name = '사원1';
```

### DELETE

```sql
DELETE FROM person;
```

### WHERE

```sql
SELECT * FROM person WHERE name = '사원1';
SELECT * FROM person WHERE name IS NOT NULL;
```

### ORDER BY

```sql
SELECT * FROM person ORDER BY age;
```

### DESK

```sql
SELECT * FROM person ORDER BY age DESC;
```

### LIKE

```sql
SELECT * FROM person WHERE name LIKE '사원%'
```

### AS

```sql
SELECT
	id AS '사원 번호',
	name AS '사원 이름',
FROM person;
-- | 사원 번호 | 사원 이름 |
-- | 1         | 사원1     |
-- | 2         | 사원2     |

SELECT
	id '사원 번호',
	name '사원 이름',
FROM person;
```

### CASE

```sql
SELECT name, salary,
CASE
    WHEN salary < 3000000 THEN '신입'
    WHEN salary >= 3000000 AND salary < 4000000 THEN '사원'
    WHEN salary >= 4000000 AND salary < 5000000 THEN '대리'
    WHEN salary >= 5000000 AND salary < 7000000 THEN '과장'
    ELSE '임원'
END AS position
FROM person;
```

### round()

```sql
SELECT round(12.34567, 2) -- 12.34
```

### substr()

```sql
SELECT substr('abcdefg', 2); -- bcdefg
SELECT substr('abcdefg', 3, 4); -- cdef
```

### strftime()

```sql
SELECT strftime('%Y-%m-%d %H:%M:%S', 'now', 'localtime');
```

## 기타

### PRAGMA

```sql
PRAGMA table_info(person);
```

### EXPLAIN QUERY PLAN

```sql
-- 이렇게 입력하면 SQLite가 어떻게 검색할지를 알려줘요.
EXPLAIN QUERY PLAN SELECT * FROM person WHERE name = '사원1';
```

