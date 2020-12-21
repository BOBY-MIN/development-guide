### 데이타베이스 관리

> 생성

```
CREATE DATABASE [db_name] DEFAULT CHARACTER SET utf8;
```

> 삭제

```
DROP DATABASE [db_name];
```

***

### 테이블 스페이스 관리

> 생성

```
CREATE TABLESPACE [TABLESPACE_NM]
    ADD DATAFILE 'DE001.TSDE01.ibd'
    Engine=InnoDB;
```
* DATAFILE 경로까지 지정 가능 '/testdir/datafilenm' 디렉토리 우선 생성 필요

> 삭제

```
DROP TABLESPACE [TABLESPACE_NM]
```

***

### ROLE 관리

> 생성

```
CREATE ROLE 'role_name1'@'host', 'role_name2'@'host';
CREATE ROLE 'role_name1'@'%', 'role_name2'@'%';
```
* 외부 IP 접속 필요 시 '%' 도 생성('%' 모든 ip 허용)
* 'role_name'@'host' 작성 (host : localhost or ip)

> 삭제

```
DROP ROLE 'role_name1'@'host', 'role_name2'@'host';
```

> 권한 부여

```
GRANT ALL ON [db_name].* TO 'role_name'@'host';
GRANT ALL ON [db_name].[tbl_nm] TO 'role_name'@'host';

GRANT SELECT ON [db_name].* TO 'role_name'@'host';
GRANT INSERT, UPDATE, DELETE ON [db_name].* TO 'role_name'@'host';
```

> 권한 제거

```
REVOKE 'role_name'@'hos' FROM 'user_name'@'host';
```

### 유저 관리

> 생성

```
CREATE USER 'user_name'@'host' IDENTIFIED BY 'password' DEFAULT ROLE 'role_name'@'host';
```

* 유저 권한 부여 후 아래 명령어 수행해야 즉시 적용 됨
  ```
  FLUSH PRIVILEGES;
  ```

> 삭제

```
DROP USER 'user_name'@'host';
```
