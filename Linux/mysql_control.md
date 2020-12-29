
$$$ mysql 조작


> mysql server (root)

```
-- mysql 서비스 시작
$ systemctl start mysqld.service

-- mysql 서비스 종료
$ systemctl stop mysqld.service

-- mysql 서비스 재시작
$ systemctl restart mysqld.service

-- mysql 상태 확인
$ systemctl status mysqld.service

-- 부팅 시 자동 시작 등록
$ systemctl enable mysqld.service
```

> mysql server (일반계정)

```
-- mysql 서비스 시작


-- mysql 서비스 종료


-- mysql 서비스 재시작


-- mysql 상태 확인


-- 부팅 시 자동 시작 등록

```


> mysql 접속

```
-- 접속하기
$ mysql -u root -p

-- mysql 상태 확인
$ status

-- 접속해제
$ quit
```
