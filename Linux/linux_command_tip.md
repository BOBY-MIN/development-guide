### linux 각종 팁 모음

> export

```
export PATH=test
PATH=ADD$PATH
```

* export 로 환경변수 최초 추가 후에는 PATH 로 지정
* export PATH로 계속 추가할 경우 경로가 중복되어 추가됨.
* PATH 지정 시 최종값이 변수로 지정되므로 이전 $PATH 값 추가 필요

> source ~/.bash_profile

* 프로필에 지정한 값 바로 적용을 위해서는 source 명령어 사용

```
alias tomcat="ps -ef | grep tomcat"

echo ""
echo ""
echo "------------------tomcat alias--------------------------------------------"
echo "tomcat='ps -ef | grep tomcat'"
echo ""


출력 예
------------------tomcat alias--------------------------------------------
tomcat='ps -ef | grep tomcat'

```

* 위 내용 ~/.bash_profile 작성 시 해당 계정 로그인 시 자동으로 출력되고, alias로 지정한 명령어 단축어로 수행가능


> netstat

```
tcp        0      0 domain:ssh       ip:57188    ESTABLISHED
tcp        0      1 domain:48888     ip:mysql     SYN_SENT
tcp        0     64 domain:ssh       ip:64950    ESTABLISHED
tcp        0      0 domain:57428     ip:9973       TIME_WAIT


ESTABLISHED : 서로 연결된 상태
SYN_SENT : 요청 후 대기중
TIME_WAIT : 대기상태

```

> telnet

```
-- 접속 권한 있는 지 확인할 떄 사용
$ telnet ip port
```
