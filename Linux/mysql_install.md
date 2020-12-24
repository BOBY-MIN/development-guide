
### centos7 mysql 인스톨

* centos7 은 red hat enterprise linux 7 과 동일함.

> 세부 스펙확인

```
# getconf LONG_BIT
# echo $HOSTTYPE
```

* 서버 세부 스펙 확인(arm인지 몇 bit 인지)

> mysql 설치

```
-- mysql 다운로드
# wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

-- root 계정 접속
# sudo - i

-- mysql 패키지 설치
# yum localinstall mysql-community-server-8.0.18-1.el7.x86_64.rpm

-- 패키지 설치 항목 확인
# yum repolist enabled | grep "mysql.*-community.*"

-- mysql 설치
# yum install mysql-community-server mysql mysql-libs mysql-devel mysql-server

-- mysql version 확인
# mysql --version

```

> 임시 비밀번호 확인

```
-- mysql 서비스 시작
# systemctl start mysqld.service

-- 임시 비밀번호 확인
grep 'temporary password' /var/log/mysqld.log
```

> 설정 변경

```
-- 설정 변경 파일 열기
# vi /etc/my.cnf
```
