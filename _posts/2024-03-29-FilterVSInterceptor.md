---
layout: single
title:  "Filter VS Interceptor"
categories: coding
tag: [spring, backend]
toc: true
---

## 필터링이 왜 필요한가?

개발자로서 무언가를 개발하면서 피하고 싶은 일 중 하나는 반복적으로 수행하는 작업을 
개발자가 직접 코드로 입력고 있을 때 입니다.

```
아... 이 코드 위에서 짰는데 또 자야하는거야..?
```

이럴 생각이 들 때가 많이 있죠. 보통 개발자들은 이런 반복되는 일들을 피하기 위해 클래스를 생성해서
객체화해서 반복해서 쓰거나 메소드를 지정해서 사용합니다. 이러한 중복성 제거에 특화되어 있는 기능이 있는데요.
바로 Filter 와 Interceptor입니다.

## Filter 와 Interceptor

- Filter(필터)
	필터는 일상 생활에서도 많이 볼 수 있는데요.. 에어컨 필터도 있고, 정수기 필터도 있고...
	무언가 정제된 것을 얻는다는 뜻입니다.

- Interceptor(인터셉터)
	무언가를 가로채는 녀석이죠..! 이름만 들어서는 아주 나쁜 놈 같아 보이는데요 전혀 그런 것은 아닙니다.

## Filter

Filter는 대표적으로 Spring Security에서 많이 사용하기 때문에 당연히 Spring Framework에 포함되는 스펙일 것이라 생각하시는 분들이 많을텐데요.
사실 Filter는 J2EE(Java 2 Enterprice Edition) 표준 스펙으로 제공 주체가 Spring Framework가 아닌 Java입니다. 이에 대한 내용은 아래의 오라클 공식문서에서 확인하실 수 있습니다.
https://docs.oracle.com/cd/A97329_03/web.902/a95878/filters.htm

![filter](https://velog.velcdn.com/images/yelosta/post/84faba5a-430a-4831-bcd0-540fced97330/image.png)


위의 사진을 보시면 왼쪽 프로세스에는 필터가 없고 오른쪽 프로세스에는 필터가 있습니다.
filter1, filter2, ..., filterN 을 보시면 하나의 시퀀스를 이루고 있죠 이것을 filter chain이라고 부릅니다.
이러한 순서들은 web.xml 파일에 정의되어 있으며, Request(요청) 시에는 filter1 부터 호출이 되고, Response(응답) 시에는 filterN부터 호출이 됩니다.

Filter의 대표적인 역할로는 인코딩 변환 처리, XSS 방어, Log, 인증 등에 사용이 됩니다.
아래의 사진처럼 인코딩 변환처리를 해줄 수 있습니다.

![web.xml](https://velog.velcdn.com/images/yelosta/post/62e773e8-95c7-4365-b987-62cfef633aae/image.png)

### 필터의 메소드

```java
package com.acme.filter;
import javax.servlet.*;

public class GenericFilter implements javax.servlet.Filter {
	public FilterConfig filterConfig;
    
	public void doFilter(final ServletRequest request,
    					  final ServletResponse response, FilterChain chain)
                          throws java.io.IOException, javax.servlet.ServletException {
		chain.doFilter(request,response);    	                      
    }
    
    public void init(final FilterConfig filterConfig) {
    	this.filterConfig = filterConfig;
    }
    
    public void destory() {
    }
}
```

javax.servlet.Filter 를 상속받고 있는 Filter 예제입니다.
- _init_

	FilterConfig(필터 정보)를 담고 있는 객체를 파라미터로 받는 메소드입니다. xml에 등록한 정보들이 FilterConfig에 담고 있어서 초기화 시에 사용이 가능합니다.
- _doFilter_

	분리된 관심사항이 있는 메소드이며, 파라미터를 보면 FilterChain(Filter가 여러 개모여 형성된 체인)이 있습니다. **chain.doFilter(request, response)** 메소드는 다음 Filter로 넘어가기 위해 꼭 명시를 해줘야하는 메소드입니다.
- _destory_

	init이 생성될 때 한번만 호출되는 메소드라면 destory는 소멸될 때 한번만 호출됩니다. 
    ex) 필터 소멸 → 커넥션 풀 닫힘 → WAS 닫힘

## Interceptor

이제 Filter에 대해서 어느 정도 감이 잡히시나요? 그렇다면 Interceptor는 무엇일까요?
한번 [스프링 공식 문서](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerInterceptor.html)를 살펴볼까요?

![spring-HandlerInteceptor](https://velog.velcdn.com/images/yelosta/post/1696fe4b-f0a1-4d70-a0c0-84dbf01ac80b/image.png)

위의 사진과 같이 **package.org.springframework.web.servlet** 에 속한다는 것을 알 수 있습니다. 즉, Interceptor는 filter와는 달리 spring framework에서 제공하며, 디스패처 서블릿이 컨트롤러를 호출하기 전/후 요청에 대해 부가적인 작업을 처리하는 객체입니다.

### 인터셉터의 메소드
인터셉터에는 3가지의 메소드가 있습니다.

- prehandle
  ![prehandle](https://velog.velcdn.com/images/yelosta/post/3384c103-cbb9-4644-89a4-bcbd263bfdd3/image.png)
  핸들러가 실행되기 전에 실행되는 메소드로 비즈니스 로직에서 공통적으로 처리해야 할 사항을 처리합니다.

- posthandle
  ![posthandle](https://velog.velcdn.com/images/yelosta/post/fc5da59d-6a47-443b-956d-7beec8d85509/image.png)
  핸들러가 실행 된 이후에 실행되는 메소드로 파라미터로 ModelAndView를 받는 것을 볼 수 있는데 모델에 대해서 추가적인 작업이 필요할 때 사용할 수 있습니다.

- afterCompletion
  ![afterCompletion](https://velog.velcdn.com/images/yelosta/post/b31abeca-4942-40e0-9341-d2567fa2c419/image.png)
  역시 핸들러 이후에 실행되는 메소드로 파라미터로 Exception을 받으므로 비즈니스 로직에서 에러가 발생한 부분을 처리할 때 사용할 수 있으며, 리소스 정리할 때도 사용이 가능합니다.

이 3개의 메소드는 default 메소드로 사용하고 싶은 메소드만 선택해서 사용하면 됩니다.

### 인터셉터 동작 프로세스
핸들러 조회 → 알맞은 핸들러 어댑터 불러옴 → preHandle → 핸들러 어댑터를 통해 핸들러 실행(비즈니스 로직 실행) → postHandle → view 관련 처리 → afterCompletion

## 요약

> 그래서 Filter와 Interceptor의 차이가 뭔가요?

|Filter|Interceptor|
|---|---|
|자바 표준 스펙|스프링 제공 기술|
|다음 필터를 실행하기 위해서는 개발자가 명시적으로 작성(doFilter)|다음 인터셉터를 실행하기 위해 개발자가 신경 써야 하는 부분 없음|
|ServletRequest, ServletResponse를 필터 체이닝 중간에 새로운 객체로 변경 가능|ServletRequest, ServletResponse를 필터 체이닝 중간에 새로운 객체로 변경 불가|
|필터에서 예외 발생하면 @ControllerAdvice에서 처리 불가|인터셉터에서 예외가 발생하면 @ControllerAdvice에서 처리 가능|

👀 @ControllerAdvice의 적용 범위는 Dispatcher Servlet이므로 Dispatcher Servlet이 실행되기 이전에 동작하는 Filter는 예외가 발생하면 @ControllerAdvice에서 처리가 불가능합니다. 












