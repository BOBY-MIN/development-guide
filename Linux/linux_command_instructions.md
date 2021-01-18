
### linux 자주 사용하는 명령어 정리

* man 명령어 : 명령어 메뉴얼 보기(옵션값 확인 시 사용)

> 서비스 확인

```
netstat -tnlp : 구동중인 서비스 상태 확인
netstat -ntp : 현재 서비스 연결상태 확인
```


> vi 명령어

```
$ w : 저장
$ wq : 저장 후 종료
$ q : 종료(저장X)

$ set nu : vi 라인 번호 출력
$ set nonu : vi 라인 번호 출력 취소

$ :/text : 아래방향탐색
$ :?text : 위방향탐색

$ n : 다음찾기
$ N : 이전찾기

$ gg : 맨 위로 이동
$ G : 맨 아래로 이동

$ u : 실행취소
$ ctrl + r : 재실행

$ i : 입력모드
$ o : 아래 한줄 띄우고 입력모드
$ O : 위 한줄 띄우고 입력모드

$ dd : 한줄삭제
$ c : 잘라내기
$ y : 복사
$ yy : 한줄 복사
$ p : 붙여넣기
```

> 권한

```
$ vi /etc/passwd : 현재 계정정보들 조회
  root:x:0:0:root:/root:/bin/bash
  root - 사용자계정
  x - 비번
  0 - UID(숫자로 된 사용자ID)
  0 - GID(숫자로 된 그룹ID)
  root - 코멘트
  /root - 로그인 성공한 후 위치할 홈 디렉토리명
  /bin/bash - 명령어를 처리하는 쉘의 종류

$ su : 계정 전환
$ su - : 계정 전환 + 계정의 환경변수 적용
$ sudo -i : 다른 계정의 권한 가져오기
$ whoami : 현재 계정 정보 확인

$ chmod -R 777 디렉토리명/파일명 : 권한 부여
  rwxr-xr-x : 3자리씩 끊어서 소유자/그룹/전체 권한
  r: 읽기 4
  w: 쓰기 2
  x: 실행 1
  -R: 하위 디렉토리에도 권한 똑같이 부여

$ chown 소유자명:소유자그룹명 디렉토리명/파일명
  : 소유자 변경

```


> 파일/디렉토리

```
$ wget [옵션]... [URL]...
  : web get의 약어 웹 상의 파일 다운로드 시 사용

$ tar [OPTION...] [FILE]...
  : 압축/해제 시 사용

$ cp [옵션] [원본파일명] [목적 파일명 / 디렉토리명]
  : 파일 복사

$ mv [옵션] [원본파일명] [목적 파일명 / 디렉토리명]
  : 파일 이동

$ find [경로] [-name] [파일 및 디렉토리 명] [-type d/f]
  : 파일 및 디렉토리 찾기

$ mkdir [디렉토리명] / rmdir [디렉토리명]
  : 디렉토리 생성 / 삭제

$ rm [옵션] [파일명]
  : 파일 삭제
  : -rf 하위 모두 제거(디렉토리인 경우)

$ unzip [war/jar/zip] [옵션]
  : zip 해제
  : unzip ~~~.war -d /tmp : tmp 에 ~~~.war 해제

$ cat [옵션] [filename]
  : [filename] file1 file2 file3 여러 파일 한 번에 보기
  : [-n] file 번호 추가


$ grep [옵션] [정규표현식(문자열)] [찾기 대상이 될 파일명]

ex)  grep -r '*찾을 문자열*' ./*

출처: https://unabated.tistory.com/entry/grep-egrep-fgrep-정규식 [랄라라]
```

> crontab

* linux 용 스케줄러로 동시 트래픽 테스트 가능
* 주기 지정 후 batch 수행 가능

```
$ crontab -e : crontab 편집
$ crontab -l : crontab 확인

```
