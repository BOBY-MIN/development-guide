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
