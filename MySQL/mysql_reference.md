### 참고 사항

* mysql 은 오라클과 달리 NULL / '' (empty string) 을 구분함

```
INSERT INTO TABLE_NM (COLUMN1, COLUMN2) VALUES ('1', '');
INSERT INTO TABLE_NM (COLUMN1, COLUMN2) VALUES ('2', NULL);

SELECT
  *
FROM
  TABLE_NM
WHERE
  COLUMN2 IS NULL

-- 오라클은 1, 2 번 다 나옴.
-- MYSQL은 2번만 나옴.
```

* 오라클과 동일한 조회결과를 얻으려면 WHERE 조건 절에 '' (empty string) 조건도 고려하거나 JAVA 단에서 PARAM 값 가져올 때 빈 값에 대해 null로 치환해주는 공통메서드를 이용해 저장 시 null 로 저장되게끔 하는게 좋음.


### oracle 대비 mysql 내장함수

> nvl

```
COALESCE('비교 컬럼', 'null 대체')
```

> to_date

```
DATE_FORMAT('컬럼', '형식')
```
* '%Y-%m-%d' : YYYY-MM-DD
* '%Y년 %m월 %d일' : YYYY년 MM월 DD일
* '%Y%m%d%H%i%s' : YYYYMMDDHHMMSS

> ||

```
CONCAT('%', 'test', '%')
```

> DATE_ADD / DATE_SUB

```
- 더하기
SELECT DATE_ADD(NOW(), INTERVAL 1 SECOND);
SELECT DATE_ADD(NOW(), INTERVAL 1 MINUTE);
SELECT DATE_ADD(NOW(), INTERVAL 1 HOUR);
SELECT DATE_ADD(NOW(), INTERVAL 1 DAY);
SELECT DATE_ADD(NOW(), INTERVAL 1 MONTH);
SELECT DATE_ADD(NOW(), INTERVAL 1 YEAR);

- 빼기
SELECT DATE_SUB(NOW(), INTERVAL 1 SECOND);
SELECT DATE_SUB(NOW(), INTERVAL 1 MINUTE);
SELECT DATE_SUB(NOW(), INTERVAL 1 HOUR);
SELECT DATE_SUB(NOW(), INTERVAL 1 DAY);
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);
SELECT DATE_SUB(NOW(), INTERVAL 1 YEAR);
```
