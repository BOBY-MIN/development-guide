
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
character-set-server=utf8md4
collation-server=utf8md4_general_ci
init_connect=SET collation_connection = utf8md4_general_ci
init_connect=SET NAMES utf8md4
```

* mysql8은 utf8md3, utf8md4 으로 구분됨. 문자셋 3byte, 4byte 구분

> 그 외

```
-- full text search 단어분리 최소자리수
[mysqld]
ft_min_word_len=2

-- linux database, table 소문자 저장 / 조회 시 대소문자 구분없이 조회
-- 설정 후 생성된 db와 table에만 적용
[mysqld]
lower_case_table_names=1


-- 최초 설정
[mysqld]
basedir=/home/was/mysql
datadir=/home/was/mysql/data

socket=/home/was/mysql/socket/mysql.sock

log-error=/home/was/mysql/log/mysqld.log
pid-file=/home/was/mysql/run/mysqld/mysqld.pid

[mysqld_safe]

log-error=/home/was/mysql/log/mysqld.log
pid-file=/home/was/mysql/run/mysqld/mysqld.pid
```
