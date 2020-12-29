
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
$ cd mysql.server 위치 경로
$ ./mysql.server start &

-- mysql 서비스 종료
$ cd mysql.server 위치 경로
$ ./mysql.server stop

-- mysql 서비스 재시작
$ cd mysql.server 위치 경로
$ ./mysql.server reload &

-- mysql 상태 확인
$ cd mysql.server 위치 경로
$ ./mysql.server status

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
