
### mySql 설정 파일 참고

* 윈도우 : my.ini (C:\ProgramData\MySQL\MySQL Server 8.0\my.ini)
* linux : my.cnf

> 설정 구역 차이

```
[mysql] : mysql command-line client
[mysqld] : mysql server 설정
```


> utf8 설정

```
-- 윈도우
[mysqld]
character-set-server = utf8

-- linux
[client]
default-character-set = utf8

[mysql]
default-character-set=utf8

[mysqldump]
default-character-set=utf8

[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
init_connect=SET collation_connection = utf8_general_ci
init_connect=SET NAMES utf8
```

> full text search 단어분리 최소자리수

```
[mysqld]
ft_min_word_len=2
```
