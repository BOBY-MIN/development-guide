### 커스텀 어노테이션 생성(ex:byte 체크)

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
