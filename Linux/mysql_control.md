
### mysql 조작


> mysql server

```
-- mysql 서비스 시작
# systemctl start mysqld.service

-- mysql 서비스 종료
# systemctl stop mysqld.service

-- mysql 서비스 재시작
# systemctl restart mysqld.service

-- mysql 상태 확인
# systemctl status mysqld.service

-- 부팅 시 자동 시작 등록
# systemctl enable mysqld.service
```

> mysql 접속

```
-- 접속하기
# mysql -u root -p

-- mysql 상태 확인
# status

-- 접속해제
# quit
```
