
$$$ centos7 mysql 인스톨(일반계정)

* centos7 은 red hat enterprise linux 7 과 동일함.
* 설치 전 boost 1.7 이상, gcc 5.4 이상, cmake 3.15 이상 설치 필요
* ncurses-devel 라이브러 설치 필요

> 세부 스펙확인

```
$ getconf LONG_BIT
$ echo $HOSTTYPE
```

* 서버 세부 스펙 확인(arm인지 몇 bit 인지)

> mysql 설치

```
-- mysql 다운로드
$ wget https://cdn.mysql.com/archives/mysql-8.0/mysql-boost-8.0.18.tar.gz
  : sourcecode -> Generic Linux (Architecture Independent), Compressed TAR Archive, Includes Boost Headers 로 설치
  : 소스코드는 플랫폼에 의존적이지 않으므로 centos7이더라도 generic linux 무방함.

-- 압축 해제 후 기본 디렉토리 생성
$ tar -zxvf filename.tar.gz
  : 기본 디렉토리는 mysql 설치파일과 다른 곳에 지정
  : 기본 디렉토리는 mysql 이 실제로 설치될 장소를 뜻함.

$ cd mysql-8.0.18
$ mkdir build_target
$ cd build_target
  : cmake는 컴파일된 오브젝트 파일만 별도로 관리하므로 하위 디렉토리 생성 후 cmake 수행

$ cmake .. -DCMAKE_INSTALL_PREFIX=/home/was/mysql -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/home/was/install/mysql-8.0.18/boost/boost_1_70_0 -DMYSQL_UNIX_ADDR=/home/was/mysql/socket/mysql.sock -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_EXTRA_CHARSETS=all -DMYSQL_DATADIR=/home/was/mysql/data -DENABLED_LOCAL_INFILE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DMYSQL_TCP_PORT=3306 -DENABLE_DOWNLOADS=1

-- cmake 수행 후 make 파일 생성되면 아래 명령어로 설치
$ make && make install


-- 초기화 설정 1
$ vi ~/.bash_profile
  export MYSQL_HOME=/home/was/mysql
  export PATH=$MYSQL_HOME/bin:$PATH
  export LD_LIBRARY_PATH=$MYSQL_HOME/lib:$LD_LIBRARY_PATH

$ cd /home/was/mysql/support-files
$ cp mysql.server /home/was/mysql/etc/rc.d/init.d/mysqld

$ cd /home/was/mysql/mysql-test/include
$ cp default_mysqld.cnf /home/was/mysql/etc/my.cnf


-- 초기화 설정2
$ vi /home/was/mysql/etc/rc.d/init.d/mysqld
  basedir=/home/mysql/mysql/local/mysql
  datadir=/home/mysql/mysql/data
  # Get arguments from the my.cnf file,
  conf=/home/mysql/mysql/etc/my.cnf

$ vi /home/was/mysql/etc/my.cnf
  : my.md 참고


-- mysql 초기화(mysql 설치 경로에서 수행)
$ bin/mysqld --user=was --initialize-insecure --default-file=/home/was/mysql/etc/my.cnf --lower_case_table_names=1 --console
  : mysql_isntall_db deprecated 된 후 --initialize 로 초기화
  : --lower_case_table_names=1 테이블명 대소문자 구분없도록 함.

-- mysqld 실행
$ cd /mysql/supprot-files
$ ./mysql.server start --skip-grant-tables &
  : --skip-grant-tables 로 실행 안하면 초기에는 error 발생함.
  : my.cnf 내 datadir, socket, pid-file, log-error 디렉토리 경로 미리 생성 필요
  : & 백그라운드로 실행

$ mysql 로 접속 가능
$ show databases;
  : mysql 접속 후 mysql db 내 table 모두 수동으로 생성 필요
  : mysql.user 에 mysql.infoschema, mysql.session, mysql.sys, root localhost 권한 부여 필요 -> 비번 없이 insert 후 alter 로 비번 변경(user_db_role_ddl.md 참고)


```


> 출처

```
  https://dev.mysql.com/doc/internals/en/cmake.html
  real mysql, 이성욱 저
  https://dev.mysql.com/doc/refman/8.0/en/data-directory-initialization.html
  https://mozi.tistory.com/78
```


<br>
<br>
<br>
<br>

> 작업 중 발생한 에러

* 1차 에러

```
It fails with the following output:

Change Dir: /home/was/install/mysql-8.0.18/build_target/CMakeFiles/CMakeTmp

Run Build Command(s):/usr/bin/gmake cmTC_d1565/fast && /usr/bin/gmake  -f CMakeFiles/cmTC_d1565.dir/build.make CMakeFiles/cmTC_d1565.dir/build
gmake[1]: Entering directory `/home/was/install/mysql-8.0.18/build_target/CMakeFiles/CMakeTmp'
Building C object CMakeFiles/cmTC_d1565.dir/testCCompiler.c.o
/usr/bin/cc   -02  -o CMakeFiles/cmTC_d1565.dir/testCCompiler.c.o -c /home/was/install/mysql-8.0.18/build_target/CMakeFiles/CMakeTmp/testCCompiler.c
cc: error: unrecognized command line option ‘-02’
gmake[1]: *** [CMakeFiles/cmTC_d1565.dir/testCCompiler.c.o] Error 1
gmake[1]: Leaving directory `/home/was/install/mysql-8.0.18/build_target/CMakeFiles/CMakeTmp'
gmake: *** [cmTC_d1565/fast] Error 2
```

```
cmake 구문 에러로 속성값 확인(위 최종 cmake 구문으로는 정상 수행됨.)
```

<br>

* 2차 에러

```
CMake Error at cmake/os/Linux.cmake:59 (MESSAGE):
  GCC 5.3 or newer is required (-dumpversion says 4.8.5)
Call Stack (most recent call first):
  CMakeLists.txt:442 (INCLUDE)
```

```
gcc 버전 업그레이드로 해결
```

<br>

* 3차 에러

```
CMake Error at cmake/boost.cmake:104 (MESSAGE):
  You can download it with -DDOWNLOAD_BOOST=1 -DWITH_BOOST=<directory>

  This CMake script will look for boost in <directory>.  If it is not there,
  it will download and unpack it (in that directory) for you.

  You can also download boost manually, from
  https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.tar.gz

  If you are inside a firewall, you may need to use an https proxy:

  export https_proxy=http://example.com:80
```

```
tar.gz 파일 boost headers 포함된 버전으로 변경하여 해결
```

<br>

* 4차 에러

```
CMake Error at cmake/readline.cmake:71 (MESSAGE): Curses library not found.  Please install appropriate package,

remove CMakeCache.txt and rerun cmake.On Debian/Ubuntu, package name is libncurses5-dev, on Redhat and derivates it is ncurses-devel.
Call Stack (most recent call first):
cmake/readline.cmake:100 (FIND_CURSES)
cmake/readline.cmake:194 (MYSQL_USE_BUNDLED_EDITLINE)
CMakeLists.txt:1185 (MYSQL_CHECK_EDITLINE)
```

```
ncurses-devel 라이브러리 설치로 해결
```

<br>

* 5차 에러

```
2020-12-29T05:03:58.201519Z mysqld_safe Logging to '/home/was/mysql/log/mysqld.log'.
2020-12-29T05:03:58.225125Z mysqld_safe Starting mysqld daemon with databases from /home/was/mysql
2020-12-29T05:03:58.235585Z 0 [Warning] [MY-010139] [Server] Changed limits: max_open_files: 1024 (requested 8161)
2020-12-29T05:03:58.235598Z 0 [Warning] [MY-010142] [Server] Changed limits: table_open_cache: 431 (requested 4000)
2020-12-29T05:03:58.497416Z 0 [System] [MY-010116] [Server] /home/was/mysql/bin/mysqld (mysqld 8.0.18) starting as process 18685
2020-12-29T05:03:58.509358Z 1 [ERROR] [MY-011011] [Server] Failed to find valid data directory.
2020-12-29T05:03:58.509611Z 0 [ERROR] [MY-010020] [Server] Data Dictionary initialization failed.
2020-12-29T05:03:58.509691Z 0 [ERROR] [MY-010119] [Server] Aborting
2020-12-29T05:03:58.510467Z 0 [System] [MY-010910] [Server] /home/was/mysql/bin/mysqld: Shutdown complete (mysqld 8.0.18)  Source distribution.
2020-12-29T05:03:58.526590Z mysqld_safe mysqld from pid file /home/was/mysql/run/mysqld/mysqld.pid ended
```

```
my.cnf 내 datadir, basedir 경로 잘못지정해서 난 에러
```

<br>

* 6차 에러

```
2020-12-29T05:22:53.271063Z mysqld_safe Logging to '/home/was/mysql/log/mysqld.log'.
2020-12-29T05:22:53.292772Z mysqld_safe Starting mysqld daemon with databases from /home/was/mysql/data
2020-12-29T05:22:53.302862Z 0 [Warning] [MY-010139] [Server] Changed limits: max_open_files: 1024 (requested 8161)
2020-12-29T05:22:53.302873Z 0 [Warning] [MY-010142] [Server] Changed limits: table_open_cache: 431 (requested 4000)
2020-12-29T05:22:53.567750Z 0 [System] [MY-010116] [Server] /home/was/mysql/bin/mysqld (mysqld 8.0.18) starting as process 20626
mysqld: Table 'mysql.plugin' doesn't exist
2020-12-29T05:22:54.606181Z 0 [ERROR] [MY-010735] [Server] Could not open the mysql.plugin table. Please perform the MySQL upgrade procedure.
2020-12-29T05:22:54.633585Z 0 [System] [MY-010229] [Server] Starting crash recovery...
2020-12-29T05:22:54.654431Z 0 [System] [MY-010232] [Server] Crash recovery finished.
2020-12-29T05:22:54.670950Z 0 [Warning] [MY-010015] [Repl] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2020-12-29T05:22:54.733364Z 0 [Warning] [MY-010015] [Repl] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2020-12-29T05:22:54.736171Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2020-12-29T05:22:54.739145Z 0 [ERROR] [MY-011811] [Server] Can't start server: can't check PID filepath: No such file or directory
2020-12-29T05:22:54.761796Z mysqld_safe mysqld from pid file /home/was/mysql/run/mysqld/mysqld.pid ended
```

```
./mysql.server start --skip-grant-tables 로 실행해서 해결
```

<br>

* 7차 에러

```
2020-12-29T05:24:23.507612Z mysqld_safe Logging to '/home/was/mysql/log/mysqld.log'.
2020-12-29T05:24:23.531233Z mysqld_safe Starting mysqld daemon with databases from /home/was/mysql/data
2020-12-29T05:24:23.541506Z 0 [Warning] [MY-010139] [Server] Changed limits: max_open_files: 1024 (requested 8161)
2020-12-29T05:24:23.541518Z 0 [Warning] [MY-010142] [Server] Changed limits: table_open_cache: 431 (requested 4000)
2020-12-29T05:24:23.804796Z 0 [System] [MY-010116] [Server] /home/was/mysql/bin/mysqld (mysqld 8.0.18) starting as process 21036
2020-12-29T05:24:24.830555Z 0 [System] [MY-010229] [Server] Starting crash recovery...
2020-12-29T05:24:24.839482Z 0 [System] [MY-010232] [Server] Crash recovery finished.
2020-12-29T05:24:24.857193Z 0 [Warning] [MY-010015] [Repl] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2020-12-29T05:24:24.887851Z 0 [Warning] [MY-010015] [Repl] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2020-12-29T05:24:24.890939Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2020-12-29T05:24:24.893059Z 0 [ERROR] [MY-011811] [Server] Can't start server: can't check PID filepath: No such file or directory
2020-12-29T05:24:24.916063Z mysqld_safe mysqld from pid file /home/was/mysql/run/mysqld/mysqld.pid ended
```

```
my.cnf 내 datadir, socket, pid-file, log-error 디렉토리 중 누락된 내역 있어서 발생
```
