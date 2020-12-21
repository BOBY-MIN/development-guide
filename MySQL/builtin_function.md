## oracle 대비 mysql 내장함수

### nvl
```
COALESCE('비교 컬럼', 'null 대체')
```

### to_date
```
DATE_FORMAT('컬럼', '형식')
```
* '%Y-%m-%d' : YYYYMMDD
* '%Y년 %m월 %d일' : YYYY년 MM월 DD일

### ||
```
CONCAT('%', 'test', '%')
```
