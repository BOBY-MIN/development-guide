
### mysql Full text index

* utf-8 인 경우만 한글 검색 적용 가능
* innodb는 5.6 이상만 가능

> global variable 변경

```
window
  :  my.ini
  [mysqld] 하단
  ft_min_word_len=2 추가

linux
  : my.cnf
  [mysqld] 하단
  ft_min_word_len=2
  [myisamchk] 하단
  ft_min_word_len=2
```

* 기본 검색 글자수를 2자로 지정
* 변경 후 mysql 재실행 필요
  + window : 실행창 -> services.msc -> mysql 찾아서 재실행

> 변수 변경 확인

```
SHOW GLOBAL VARIABLES LIKE 'ft_m%';
```

> 테이블 생성

```
-- 인덱스 생성
-- ALTER TABLE DBNAME.TABLE_NAME DROP INDEX IX01_HOMEPAGE_BOARD;
CREATE FULLTEXT INDEX IX01_HOMEPAGE_BOARD ON DBNAME.TABLE_NAME(BOARD_TITLE) WITH PARSER NGRAM;

-- 인덱스 재설정
OPTIMIZE TABLE 테이블명;
```

* PARSER NGRAM 지정해야 한국어 단어별로 쪼개서 검색 가능(단어 쪼개는 기준은 ft_min_word_len 길이)
  + ex) 안녕하세요. -> 안녕, 하세, 녕하 검색 가능
* 컬럼 , 으로 여러개 지정 가능

> 쿼리 작성

```
SELECT
  *
FROM
  TABLE_NM
WHERE
  MATCH(BOARD_TITLE) AGAINST('검색' IN NATURAL LANGUAGE MODE);
```

* AGINST option 값
  + IN NATURAL LANGUAGE MODE : 자연어 모드로 단어 쪼개는 기준으로 모두 검색(검색 결과 많아짐)
  + IN BOOLEAN MODE : 쪼개지지 않은 검색어로 제공된 단어가 포함된 경우만 검색
    - 검색어 앞에 '+' AND / '-' NOT 등 추가 속성 지정 가능
