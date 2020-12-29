
### 각종 설정 파일 모음

> /etc/sudoers

```
-- 일부 명령어만 nopasswd 지정
사용자명 ALL=NOPASSWD: 명령어1, 명령어2


```

* root 권한 관리
* visudo -f sudoers 로 편집 권장
* sudo visudo -f /mnt/etc/sudoers

<br>

> /etc/passwd

```
root:x:0:0:root:/root:/bin/bash
  root - 사용자계정
  x - 비번
  0 - UID(숫자로 된 사용자ID)
  0 - GID(숫자로 된 그룹ID)
  root - 코멘트
  /root - 로그인 성공한 후 위치할 홈 디렉토리명
  /bin/bash - 명령어를 처리하는 쉘의 종류
```

* 현재 계정정보들 조회
