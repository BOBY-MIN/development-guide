### @ControllerAdvice 를 이용한 공통 exception 처리

> servlet-context.xml

```
<context:component-scan base-package="test" />
```

* 컴포넌트 스캔을 통해 빈으로 등록 필요

> @ControllerAdvice 구현 객체

```
package test;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

import test.exception.custom.BusinessException;
import test.exception.custom.ScreenException;
import lombok.extern.slf4j.Slf4j;

@ControllerAdvice
@Slf4j
public class CommonExceptionAdvice {

	// 응답 객체 JSON 예시(ErrorResponse)
//	{
//	  "message": " Invalid Input Value",
//	  "status": 400,
//	  // "errors":[], 비어있을 경우 null 이 아닌 빈 배열을 응답한다.
//	  "errors": [
//	    {
//	      "field": "name.last",
//	      "value": "",
//	      "reason": "must not be empty"
//	    },
//	    {
//	      "field": "name.first",
//	      "value": "",
//	      "reason": "must not be empty"
//	    }
//	  ],
//	  "code": "C001"
//	}

	/**
	 * javax.validation.Valid or @Validated 으로 binding error 발생시 발생한다.
	 * HttpMessageConverter 에서 등록한 HttpMessageConverter binding 못할경우 발생
	 * 주로 @RequestBody, @RequestPart 어노테이션에서 발생
	 */
	@ExceptionHandler(BindException.class)
	protected ResponseEntity<ErrorResponse> handleBindException(BindException e) {
		log.warn("handleBindException", e);
		final ErrorResponse response = ErrorResponse.of(ErrorCode.INVALID_INPUT_VALUE, e.getBindingResult());
		return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
	}

	/**
	 * javax.validation.Valid or @Validated 으로 binding error 발생시 발생한다.
	 * HttpMessageConverter 에서 등록한 HttpMessageConverter binding 못할경우 발생
	 * 주로 @RequestBody, @RequestPart 어노테이션에서 발생
	 */
	@ExceptionHandler(MethodArgumentNotValidException.class)
	protected ResponseEntity<ErrorResponse> handleMethodArgumentNotValidExceptionException(MethodArgumentNotValidException e) {
		log.warn("MethodArgumentNotValidException", e);
		final ErrorResponse response = ErrorResponse.of(ErrorCode.INVALID_INPUT_VALUE, e.getBindingResult());
		return new ResponseEntity<>(response, HttpStatus.BAD_REQUEST);
	}

	@ExceptionHandler(BusinessException.class)
	protected ResponseEntity<ErrorResponse> handleBusinessException(final BusinessException e) {
		log.warn("handleBusinessException", e);
		final ErrorCode errorCode = e.getErrorCode();
		final ErrorResponse response = ErrorResponse.of(errorCode);
		return new ResponseEntity<>(response, HttpStatus.valueOf(errorCode.getStatus()));
	}

	@ExceptionHandler(ScreenException.class)
	protected ModelAndView handleScreenException(final ScreenException e) {
		log.warn("handleScreenException", e);

		ModelAndView modelAndView = new ModelAndView();
		modelAndView.setViewName("error_common");
		modelAndView.addObject("exception", e);

		return modelAndView;
	}

	@ExceptionHandler(Exception.class)
	protected ModelAndView handleException(Exception e) {
		log.error("handleException", e);

		ModelAndView modelAndView = new ModelAndView();
		modelAndView.setViewName("error_common");

		return modelAndView;
	}

}
```

* 페이지 이동이 동반되는 서비스에서의 에러는 ModelAndView 로 처리
* ajax 와 같은 비동기 호출 시에는 ResponseEntity<ErrorResponse> 로 처리

> ErrorCode

```
package test;

public enum ErrorCode {

	// 400 RUNTIME_EXCEPTION
	INVALID_TYPE_VALUE(400, "NRQ_100", "Invalid Type Value")


    // 500 INTERNAL_SERVER_ERROR
    ,INTERNAL_SERVER_ERROR(500, "ISE_001", "server error")
    ;

    private final String code;
    private final String message;
    private final int status;

    ErrorCode(final int status, final String code, final String message) {
        this.status = status;
        this.message = message;
        this.code = code;
    }

    public String getMessage() {
    	return this.message;
    }
    public String getCode() {
    	return this.code;
    }
    public int getStatus() {
    	return this.status;
    }
}

```

> ErrorResponse

```
package test;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

import org.springframework.validation.BindingResult;
import org.springframework.web.method.annotation.MethodArgumentTypeMismatchException;

import lombok.AccessLevel;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class ErrorResponse {

	private String message;
	private int status;
	private List<FieldError> errors;
	private String code;

	private ErrorResponse(final ErrorCode code, final List<FieldError> errors) {
		this.message = code.getMessage();
		this.status = code.getStatus();
		this.errors = errors;
		this.code = code.getCode();
	}

	private ErrorResponse(final ErrorCode code) {
		this.message = code.getMessage();
		this.status = code.getStatus();
		this.code = code.getCode();
		this.errors = new ArrayList<>();
	}

	public static ErrorResponse of(final ErrorCode code, final BindingResult bindingResult) {
		return new ErrorResponse(code, FieldError.of(bindingResult));
	}

	public static ErrorResponse of(final ErrorCode code) {
		return new ErrorResponse(code);
	}

	public static ErrorResponse of(final ErrorCode code, final List<FieldError> errors) {
		return new ErrorResponse(code, errors);
	}

	public static ErrorResponse of(MethodArgumentTypeMismatchException e) {
		final String value = e.getValue() == null ? "" : e.getValue().toString();
		final List<ErrorResponse.FieldError> errors = ErrorResponse.FieldError.of(e.getName(), value, e.getErrorCode());
		return new ErrorResponse(ErrorCode.INVALID_TYPE_VALUE, errors);
	}

	@Getter
	@NoArgsConstructor(access = AccessLevel.PROTECTED)
	public static class FieldError{

		private String field;
		private String value;
		private String reason;

		private FieldError(final String field, final String value, final String reason) {
			this.field = field;
			this.value = value;
			this.reason = reason;
		}

		public static List<FieldError> of(final String field, final String value, final String reason){
			List<FieldError> fieldErrors = new ArrayList<>();
			fieldErrors.add(new FieldError(field, value, reason));
			return fieldErrors;
		}

		private static List<FieldError> of(final BindingResult bindingResult){
			final List<org.springframework.validation.FieldError> fieldErrors = bindingResult.getFieldErrors();
			return fieldErrors.stream()
					.map(error -> new FieldError(
							error.getField(),
							error.getRejectedValue() == null ? "" : error.getRejectedValue().toString(),
							error.getDefaultMessage()))
					.collect(Collectors.toList());
		}
	}
}

```

> CustomException

```
package test;

import test.ErrorCode;

public class CustomException extends RuntimeException{

	private static final long serialVersionUID = 1L;
	private ErrorCode errorCode;

	public CustomException(String message, ErrorCode errorCode) {
		super(message);
		this.errorCode = errorCode;
	}

	public CustomException(ErrorCode errorCode) {
		super(errorCode.getMessage());
		this.errorCode = errorCode;
	}

	public ErrorCode getErrorCode() {
		return this.errorCode;
	}

}
```

* RuntimeException 은 uncheckedException 으로 해당 exception 으로 custom 하는 경우 자동 rollback됨.

<br>

출처 : <https://cheese10yun.github.io/spring-guide-exception/>
