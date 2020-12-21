## validation 용 annotation 정리

## 기본 속성 예시
```
message = "default 메시지 내용"
groups = { AdminMarker.boardRead.class, AdminMarker.boardmodify.class,}

@NotNull(message = Constants.NULL_REQUEST_BOARD_SEQ,
    groups = { AdminMarker.boardRead.class, AdminMarker.boardDelete.class })
```
* 모든 annotation에 적용 가능
* message : 체크 후 에러 메시지 출력용
* groups marker용 interface 사용하여 점검할 항목 분기 가능
  + controller 또는 service에서 체크요청 시 @Validated(AdminMarker.boardList.class) 지정

***
## annotation 예시

```
@NotNull()
```
* Integer null값 체크 시 사용가능


<br>

```
@NotEmpty()
```
* String null 체크 시 사용

<br>

```
@Size(min = 1, max = 3)
```
* "테스트" 입력될 경우 size 3으로 확인
* null 허용함
* 한글 byte 체크 원할 경우 custom annotation 생성하여 체크필요.
