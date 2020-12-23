
### @Valid 사용을 위한 dependency 추가

> pom.xml

```
<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-validation</artifactId>
		<version>2.4.0</version>
</dependency>
```

* spring validation annotation 을 위해 dependency 추가

***

### 커스텀 어노테이션 생성(ex:byte 체크)

> ByteSize

```
package com.test.www;

import static java.lang.annotation.ElementType.ANNOTATION_TYPE;
import static java.lang.annotation.ElementType.CONSTRUCTOR;
import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.PARAMETER;
import static java.lang.annotation.ElementType.TYPE_USE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

import java.lang.annotation.Documented;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import javax.validation.Constraint;
import javax.validation.Payload;

@Target({ METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER, TYPE_USE })
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = { ByteSizeValidator.class })
public @interface ByteSize {

	String message() default "byte is large";

	Class<?>[] groups() default { };

	Class<? extends Payload>[] payload() default { };

	int min() default 0;

	int max() default Integer.MAX_VALUE;
}

```
* @interface 로 어노테이션 지정
* @Constraint(validatedBy = { ByteSizeValidator.class }) 로 validation할 객체 지정
* @ByteSize(min = 5, max = 10) 과 같이 매개변수 전달 필요 시 int min() default 0; 함수로 지정


> ByteSizeValidator

```
package com.test.www;

import java.io.UnsupportedEncodingException;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class ByteSizeValidator implements ConstraintValidator<ByteSize, String>{

	private int min;
	private int max;

	@Override
	public boolean isValid(String value, ConstraintValidatorContext context) {

		log.debug("ByteSizeValidator.isValid() 호출");

		// UTF-8로 변경하여 length 체크
		int valueUtf8Length = 0;
		byte[] valueByte = null;

		try {

			valueByte = value.getBytes("utf-8");

			valueUtf8Length = valueByte.length;

		} catch (UnsupportedEncodingException e) {
			log.warn("ByteSizeValidator.isValid() UnsupportedEncodingException " + e);
			valueUtf8Length = max + 1;
		}

		// min 보다 작거나 max 보다 크면 실패
		if(valueUtf8Length > max || valueUtf8Length < min) {
			return false;
		}else {
			return true;
		}
	}

	@Override
	public void initialize(ByteSize constraintAnnotation) {

		// annotation에 지정한 값 전역변수로 지정하여 사용
		min = constraintAnnotation.min();
		max = constraintAnnotation.max();

		ConstraintValidator.super.initialize(constraintAnnotation);
	}

}
```
* initialize 오버라이드 하여 전역변수로 매개변수 지정 가능
* @ByteSize 지정된 필드의 값은 isValid() 의 value 매개변수로 인입됨.
* return false : 검증 실패 return true : 검증 성공
* 위 예시는 입력된 값 utf-8로 변환하여 byte 검증하는 로직

***

### validation annotation 활용

> controller or service

```
@Valid() AdminBoardVo adminBoardVo
@Validated(AdminMarker.boardList.class) AdminBoardVo adminBoardVo
```

* @Validated(AdminMarker.boardList.class) 지정 시 groups 에 지정된 클래스가 동일한 필드만 검증함.

> vo

```
@NotNull(message = Constants.NULL_REQUEST_BOARD_SEQ, groups = { AdminMarker.boardRead.class,
			AdminMarker.boardDelete.class, AdminMarker.boardList.class })
```

* message 지정 시 에러문구 변경 가능
* groups 지정된 클래스가 동일한 경우 검증

> marker class 생성 예

```
public class AdminMarker {

	public interface boardCreate{};
	public interface boardModify{};

}
```

***

### Exception 처리

> exception 직접 처리

```
@Validated(AdminMarker.boardList.class) AdminBoardVo adminBoardVo, BindingResult bindingResult


// validation 체크된 경우 screenException 발생시킴.
	if (bindingResult.hasErrors()) {
		throw new ScreenException(bindingResult.getFieldError().getDefaultMessage(),
				ErrorCode.INVALID_INPUT_VALUE);
	}
```

* 메서드 인자값 지정 시 검증 객체 뒤에 BindingResult 지정하면  exception 내용 담김
* 메서드 내부에서 bindingResult.hasErrors() 로 exception 처리 가능

> @ControllerAdvice 로 처리

```
@ExceptionHandler(BindException.class)
```

* BindException 발생하므로 해당 익셉션으로 핸들링
* 공통으로 처리하므로 최초 구성 후 신경 쓸 필요 없음.

***

### custom validation 추가
Validator 인터페이스 구현(List<?> 객체 검증을 위한)

> CustomCollectionValidator

```
package test;

import java.util.Collection;

import javax.validation.Validation;

import org.springframework.stereotype.Component;
import org.springframework.validation.Errors;
import org.springframework.validation.Validator;
import org.springframework.validation.beanvalidation.SpringValidatorAdapter;

@Component
public class CustomCollectionValidator implements Validator {

	// VO에 지정된 validation 제약조건을 그대로 준용하기 위해
	private SpringValidatorAdapter validator;

    public CustomCollectionValidator() {
        this.validator = new SpringValidatorAdapter(
                Validation.buildDefaultValidatorFactory().getValidator()
        );
    }
	//

    @Override
    public boolean supports(Class<?> clazz) {
        return true;
    }

    @Override
    public void validate(Object target, Errors errors) {
        if(target instanceof Collection){
            Collection<?> collection = (Collection<?>) target;

            for (Object object : collection) {
                validator.validate(object, errors);
            }
        } else {
            validator.validate(target, errors);
        }

    }

}

```

> Controller or Service 사용 예

```
	// 메서드 인자값으로 전달된 List 객체를 검증
	customCollectionValidator.validate(memberDtos, bindingResult);

    if (bindingResult.hasErrors()) {
        throw new BindException(bindingResult);
    }
```

* custom 객체에 정의된 validation 제약 조건을 그대로 사용 가능


* 출처

1. https://github.com/HomoEfficio/dev-tips/wiki/SpringMVC%EC%97%90%EC%84%9C-Collection%EC%9D%98-Validation

2. <https://gompangs.tistory.com/entry/Spring-Valid-Collection-Validation-%EA%B4%80%EB%A0%A8>
