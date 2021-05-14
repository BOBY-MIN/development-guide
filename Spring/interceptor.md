
### 인터셉터 적용

> servlet-context.xml

```
<beans:bean id="loggingInterceptor" class="test.common.interceptor.LoggingInterceptor"></beans:bean>

<interceptors>

		<interceptor>
			<mapping path="/**"/>
			<exclude-mapping path="/auth/**"/>
			<exclude-mapping path="/resources/**"/>
			<beans:ref bean="loggingInterceptor"/>
		</interceptor>

</interceptors>
```

* mapping path /** 모든 호출에 적용
* exclude-mapping path 인터셉터 미적용 서비스 기재
	+ css와 같은 정적파일 로드 시에도 interceptor 적용되므로 인터셉터 불필요 시 정적파일 경로도 제외항목에 추가
* Namespaces 에 mvc 추가 필요


> LoggingInterceptor

```
package test;

import java.net.Inet4Address;
import java.net.UnknownHostException;
import java.sql.Timestamp;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class LoggingInterceptor extends HandlerInterceptorAdapter {

  private static String makeThreadId(String hostName) {

      logn threadId = Thread.currentThread().getId()

      return hostName + Long.toString(threadId);
  }

	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		log.debug("LoggingInterceptor 확인");

		String hostName = "";

		try {

			hostName = Inet4Address.getLocalHost().getHostName();

		} catch (UnknownHostException e) {
			log.warn("hostname UnknownHostException " + e);

			// 에러 발생 시 unknown으로 defualt 값 셋팅
			hostName = "unknown";
		}

		Thread thread = Thread.currentThread();

		thread.setName(makeThreadId(hostName));

		return true;
	}

}

```

* 필요 로직 추가 가능
