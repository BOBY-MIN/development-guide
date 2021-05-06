
> CORS 정책 Access-Control-Allow-Origin

* spring-security CORS 정책 설정 내용도 추가 확인(https://vvshinevv.tistory.com/62)


#### charset 과 encoding (urlEncoding / Base64Encoding)


> urlEncoding (percent-encoding)

```
 : URL 에 문자를 표현하는 문자 인코딩 방법
 : 알파벳이나 숫자 등 몇몇 문자를 제외한 값은 옥텟 단위로 묶어서 16진수 값으로 인코딩 함.


-- 위키백과 변환 예시
 인코딩	       UTF-8	                                EUC-KR
 16진수 표현	 EC 9C 84 ED 82 A4 EB B0 B1 EA B3 BC	     C0 A7 C5 B0 B9 E9 B0 FA
 퍼센트 인코딩	%EC%9C%84%ED%82%A4%EB%B0%B1%EA%B3%BC	  %C0%A7%C5%B0%B9%E9%B0%FA
```

<img src="../img/percentEncoding.PNG" width="600px" height="200px" title="px(픽셀) 크기 설정" alt="RubberDuck"></img><br/>

* 출처 : https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%84%BC%ED%8A%B8_%EC%9D%B8%EC%BD%94%EB%94%A9


> Base64Encoding

```
 : 8비트 이진 데이터(실행파일이나 ZIP파일 등)를 문자코드에 영향을 받지 않는 공통 ASCII 영역의 문자들로만 이루어진 일련의 문자열로 바꾸는 인코딩 방식을 가리키는 개념
 : Base64는 여러가지 방식이 있지만 처음 62개는 알파벳 A-Z, a-z 와 0-9를 사용하고 있으면 마지막 두 개를 어떤 기호를 쓰느냐의 차이만 있음.


-- MIME
 : Base64 정의 기준 중 하나로 임의의 바이트 스트림을 화면에 표시할 수 있는 ASCII 문자들로 바꾸는 인코딩 방식을 말함. 이 인코딩 기준은 인터넷 전자 메일을 전송할 때 MIME의 content transfer encoding의 하나로 정의됨.
 : 인코딩된 문자열은 알파벳 대소문자와 숫자, 그리고 '+', '/' 기호 64개로 이루어지며 '=' 는 끝을 알리는 코드로 쓰임. ( '=' 기호는 끝에 자릿수가 모자르는 경우 '==' 와 같이 여러개를 넣어서 자릿수를 맞춤)
 : Base64 로 바꾸는 과정은 다음과 같음.
  1. 24비트 버퍼에 위쪽부터 한 바이트씩 세 바이트를 집어넣음.
  2. 남은 바이트가 3바이트 미만이라면, 버퍼의 남은 부분은 0으로 채워넣음.
  3. 버퍼의 위쪽부터 6비트씩 잘라 그 값을 읽어, 다음에 정렬된 64개의 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/' 문자 중에서 읽은 값에 해당하는 문자를 골라 출력함.
    if) 입력된 바이트가 하나라면 출력 중 두 개만이 사용되고 나머지 둘은 '='으로 패딩됨.
    if) 입력된 바이트가 둘이라면 출력 중 세 개만이 사용되고 나머지 하나는 '='으로 패딩됨.
    -> 위 처리는 원본으로 되돌릴 때 원본에는 없던 비트가 생기는 것을 방지하기 위함임. 이 과정을 입력데이터가 끝날 때까지 반복하면서 인코딩이 된다.

 : 변환 예시
  Man is distinguished, not only by his reason, but by this singular passion from other animals,
  which is a lust of the mind, that by a perseverance of delight in the continued and
  indefatigable generation of knowledge, exceeds the short vehemence of any carnal pleasure.
  ->
  TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5IGhpcyByZWFzb24sIGJ1dCBieSB0
  aGlzIHNpbmd1bGFyIHBhc3Npb24gZnJvbSBvdGhlciBhbmltYWxzLCB3aGljaCBpcyBhIGx1
  c3Qgb2YgdGhlIG1pbmQsIHRoYXQgYnkgYSBwZXJzZXZlcmFuY2Ugb2YgZGVsaWdodCBpbiB0
  aGUgY29udGludWVkIGFuZCBpbmRlZmF0aWdhYmxlIGdlbmVyYXRpb24gb2Yga25vd2xlZGdl
  LCBleGNlZWRzIHRoZSBzaG9ydCB2ZWhlbWVuY2Ugb2YgYW55IGNhcm5hbCBwbGVhc3VyZS4=

```

<img src="../img/base64Encoding1.PNG" width="600px" height="200px" title="px(픽셀) 크기 설정" alt="RubberDuck"></img><br/>

<img src="../img/base64Encoding2.PNG" width="400px" height="600px" title="px(픽셀) 크기 설정" alt="RubberDuck"></img><br/>

* 첫 번째 표의 index가 19이므로 T문자로 변환됨.  

* 출처 : https://ko.wikipedia.org/wiki/%EB%B2%A0%EC%9D%B4%EC%8A%A464
