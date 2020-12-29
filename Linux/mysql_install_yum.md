
$$$ centos7 mysql 인스톨

* centos7 은 red hat enterprise linux 7 과 동일함.

> 세부 스펙확인

```
$ getconf LONG_BIT
$ echo $HOSTTYPE
```

* 서버 세부 스펙 확인(arm인지 몇 bit 인지)

> mysql 설치

```
-- mysql 다운로드
$ wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm

-- root 계정 접속
$ sudo - i

-- mysql 패키지 설치
$ yum localinstall mysql-community-server-8.0.18-1.el7.x86_64.rpm

-- 패키지 설치 항목 확인
$ yum repolist enabled | grep "mysql.*-community.*"

-- mysql 설치
$ yum install mysql-community-server mysql mysql-libs mysql-devel mysql-server

-- mysql version 확인
$ mysql --version

```

> 임시 비밀번호 확인

```
-- mysql 서비스 시작
$ systemctl start mysqld.service

-- 임시 비밀번호 확인
grep 'temporary password' /var/log/mysqld.log
```

> 설정 변경

```
-- 설정 변경 파일 열기
$ vi /etc/my.cnf
```

> mysql directory 변경

```
1. mysql 서버 종료

2. /etc/my.cnf 내 하단 경로 변경
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

3. mysqld.service 파일 내 설정정보 변경
  : /usr/bin/mysqld_pre_systemd 내 /var/lib 로 시작하는 경로 모두 변경된 경로로 변경
  : user, group mysql 관리 계정으로 변경
  * 3번 항목은 서버 환경에 따라 다를 수 있음

4. 원하는 곳으로 복사
$ cp -arp 현재경로 복사경로

* 위 처리 진행 시 디렉토리 경로 권한 꼭 확인
  : drwxr-xr-x - mysql 디렉토리까지 경로의 권한이 그룹이나 타사용자에도 읽기/실행은 가능해야함.

5. 복사한 mysql 관련 파일 user, group 계정 변경(mysql 관리 계정으로)

```
