> root 계정 외부 IP허용

```
-- 사용자 확인
select host, user, plugin, authentication_string, password_last_changed from mysql.user;

-- 사용자 생성
# INSERT INTO mysql.user (host,user,authentication_string,ssl_cipher, x509_issuer, x509_subject) VALUES ('111.222.33.44','root','','','','');

-- 비밀번호 변경
# alter user 'root'@'localhost' identified WITH mysql_native_password by 'pw123';
  : mysql_native_password : 공개키 요구 안함.
  : caching_sha2_password : 공개키 요구함.

-- 권한 부여
# GRANT ALL PRIVILEGES ON *.* TO '아이디'@'111.222.33.44' WITH GRANT OPTION;
  : WITH GRANT OPTION : 부여 받은 권한을 다른 사용자에게 부여할 수 있도록 함.
  : ALL PRIVILEGES -> ALL 로 대체 가능

-- 권한 즉시 반영
# FLUSH PRIVILEGES;
```

* FLUSH PRIVILEGES; 는 중간에 한 번씩 수행해주기
* caching_sha2_password 로 설정 시에는 dataSource url 셋팅 시 아래 설정값 추가해야 함.
  + useSSL=false&allowPublicKeyRetrieval=true
* 출처 : https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_all


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
***
### 유저 관리

> 생성

```
CREATE USER 'user_name'@'host' IDENTIFIED WITH mysql_native_password BY 'password' DEFAULT ROLE 'role_name'@'host';
```

* 유저 권한 부여 후 아래 명령어 수행해야 즉시 적용 됨
  ```
  FLUSH PRIVILEGES;
  ```

> 삭제

```
DROP USER 'user_name'@'host';
```
