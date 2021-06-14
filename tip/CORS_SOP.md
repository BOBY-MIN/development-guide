
> CORS Cross Origin Recource Sharing (교차출처자원공유)

```
웹 페이지 상의 제한된 리소스를 최초 자원이 서비스된 도메인 밖의 다른 도메인으로부터 요청할 수 있게 허용하는 구조임.

- CORS 관련 HTTP 헤더
  * 요청헤더
    Origin : 요청을 보내는 페이지의 출처(도메인)
    Access-Control-Request-Method : 실제 요청하려는 메서드
    Access-Control-Request-Headers : 실제 요청에 포함되어 있는 헤더 이름

  * 응답헤더
    Access-Control-Allow-Origin : 요청을 허용하는 출처. * 이면 모든 곳에 공개되어 있음을 의미함.
    Access-Control-Allow-Credentials : 클라이언트 요청이 쿠키를 통해서 자격 증명을 해야 하는 경우에 true, true를 응답받은 클라이언트는 실제 요청 시 서버에서 정의된 규격의 인증값이 담긴 쿠키를 같이 보내야 함.
    Access-Control-Expose-Headers : 클라이언트 요청에 포함되어도 되는 사용자 정의 헤더
    Access-Control-Max-Age : 클라이언트에서 preflight의 요청 결과를 저장할 기간을 지정.
    Access-Control-Allow-Methods : 요청을 허용하는 메서드. 기본값은 GET, POST라고 보면 됨. 이 헤더가 없으면 GET과 POST 요청만 가능함. 만약 이 헤더가 지정이 되어 있으면, 클라이언트에서는 헤더 값에 해당하는 메서드일 경우에만 실제 요청을 시도하게 됨.
    Access-Control-Allow-Headers : 요청을 허용하는 헤더


- CORS 사용 종류 
  * Simple Request
    다음 3가지를 만족하는 통신 방식
      1. GET, HEAD, POST 중 한 가지 방식을 사용
      2. POST일 경우 Content-type이 아래 셋 중 하나를 만족 (application/json은 안 됨.)
        - application/x-www-form-urlencoded
        - multipart/form-data
        - text/plain
      3. 커스텀 헤더를 전송하지 않아야 함.

  * Preflight Request
    Preflight(예비) 요청을 먼저 보내고 서버가 이에 응답이 가능한 지 확인.
    예비 요청은 OPTION메서드로 HTTP 요청을 전송하고 실제 Actual 요청을 보냄.
    해당 요청에 대해서 서버가 응답하며 통신하는 형태임.

    Preflight Request는 Simple Request 에 조건에 만족하면 안되므로 GET, POST, HEAD 이 외에 메서드를 사용하는 경우에만 가능함.
    만약 POST 요청을 사용하는 경우에는 content type이 application/json이면 Preflight Request 통신이 가능함.
    또는 커스텀 헤더를 사용할 경우에 사용할 수 있음.

    예비요청/예비응답, 본요청/본응답 총 4번의 형태로 구성됨.

  * Credential
    HTTP Cookie와 HTTP Authentication 정보를 인식할 수 있게 해주는 요청.

    요청 시 xhr.withCredentials = true를 지정하는 것이 가장 큰 특징임.
    Access-Control-Allow-Credentials: true
    false인 경우 브라우저에서 요청을 거부함.

  * Non-Credential
    위 Credential 설정을 하지 않는 방식이 모두 Non-Credential에 해당함.
    Access-Control-Allow-Credentials 의 default 값이 false이므로.

** 이 외에 jsonp / --disable-web-security 옵션을 사용하는 방식도 있지만 사용하지 않는 것이 좋음.


```

* 출처 : https://ko.wikipedia.org/wiki/
https://java119.tistory.com/67


> SOP 정책 Same-Origin policy (동일출처정책)

```
주로 스크립트롭터의 데이터 접근에 적용됨.
일치하는 HTML 태그를 경유하는 이밎, CSS, 스크립트 등 출처를 경유하여 리소스를 임베드하는 것은 제한되지 않음.

프로토콜, 호스트, 포트가 동일한 경우 통신 가능하며, 이 중 하나라도 다른 경우 통신이 불가능함.

자바스크립트 엔진 표준스펙으로 서버 간 통신에서는 적용되지 않음.

이 정책을 무시하기 위해서는 CORS 를 도입하여야 함.

```

* 출처 : https://ko.wikipedia.org/wiki/
https://java119.tistory.com/67
