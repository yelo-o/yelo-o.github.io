---
layout: single
title:  "Servlet과 Dispatcher Servlet"
categories: coding
tag: [backend, servlet, spring]
toc: true
---

# 초기의 웹 프로그래밍
웹 프로그래밍 초기에는 **Client** 와 **WebServer** 가 서로 요청과 응답을 주고 받는 구조였습니다. 클라이언트가 웹 페이지를 요청하면 웹 서버는 정적인 파일만 줄 수 있었죠.
![](https://velog.velcdn.com/images/yelosta/post/ea371cad-45e2-4e50-aeef-bb3db172bfc4/image.png)
하지만 지금 현재의 웹사이트들을 보면 알 수 있듯이 동적인 웹 페이지가 필요했습니다.
사용자의 요청에 따른 응답을 다양한 언어로 구현된 프로그래밍과 통신을 할 수 있어야 하게 된 것이었죠.
그렇게 만들어진 것이 CGI 구현체입니다.
## CGI(Common Gateway Interface) 
HTTP 프로토콜 기반의 웹 서버와 다양한 언어로 구현된 프로그램 간의 데이터를 교환하는 표준 스펙, 인터페이스입니다.
![](https://velog.velcdn.com/images/yelosta/post/e0dc7c6d-80d2-4f01-9fd9-c9de21c40fc6/image.png)
다만, 여기에는 두 가지의 문제점이 있었습니다.
- 매 요청이 들어올 때마다 프로세스가 생성
- 또 그 프로세스마다 구현체가 생성
![](https://velog.velcdn.com/images/yelosta/post/fe99c88f-34fb-4fbb-84fa-6b5014090506/image.png)


### 1. 프로세스 → 스레드
![](https://velog.velcdn.com/images/yelosta/post/b4c57b06-af56-4930-9904-e607e55f4913/image.png)
프로세스를 스레드로 변경하였습니다.
메모리를 공유하는 Thread에 비해서 Process는 각자의 메모리 공간을 지니기 때문에 상대적으로 무겁고 생성되는데 시간이 오래걸립니다.
즉, 이는 많은 처리 시간을 소모하게 만듭니다.

### 2. Servlet의 도입
![](https://velog.velcdn.com/images/yelosta/post/1496d576-663c-4050-9445-9a7c2a42acc1/image.png)
Servlet은 웹 애플리케이션을 개발하기 위한 표준 스펙이자 인터페이스입니다. CGI 구현체와 마찬가지로 CGI 규약을 따르지만 이를 Servlet Container에게 위임하였고 Container와 Servlet 구현체 간에는 규칙이 존재합니다.
- Servlet 인스턴스는 Servlet Container를 통해 등록되고 사이클이 관리됩니다.
- Serlvet은 모든 요청을 받을 수 있으며, 각 요청마다 스레드가 생성되거나 기존에 생성된 스레드를 스레드 풀에서 꺼내와 동작합니다.
- Servlet 구현체는 싱글톤 패턴을 적용하였기 때문에 하나의 구현체를 통해 동작이 가능합니다.

# Servlet
## Web Container (Servlet Container)
Web Container는 Sevlet Container 라고도 불리며, 하는 일은 아래와 같습니다.
- Servlet 라이프 사이클 관리
- Client의 웹 요청을 해석하여 적정한 Servlet 구현체의 메소드를 ServletRequest, ServletResponse 매개변수와 함께 호출
![](https://velog.velcdn.com/images/yelosta/post/440b416b-7f30-43e4-8f06-9be39c0507b3/image.png)
브라우저를 통해 들어오는 요청을 확인해서 Web Container가 web.xml 파일에 등록된 내용을 토대로 알맞은 Servlet 객체로 라우팅해줍니다.


## Servlet Context
하나의 Servlet이 Servlet Container와 통신하기 위해 사용되는 메소드들을 관리하는 인터페이스이며 Web Container에 의해 생성됩니다. 기능은 아래와 같습니다.
- 웹 어플리케이션 전체에서 공유되는 데이터를 저장
- 세션 관리
- RequestDispatcher를 통해 다른 서블릿이나 JSP로 요청 디스패치
- 보안 및 로깅

# Dispatcher Servlet
스프링에서는 서블릿을 Dispatcher Servlet을 통해 사용하고 있습니다.
전체적인 구조는 아래와 같습니다.
![](https://velog.velcdn.com/images/yelosta/post/8d3699c9-4106-4dd3-a59b-8ab956e05693/image.png)

### Dispatcher Servlet 등록
![](https://velog.velcdn.com/images/yelosta/post/fa01c262-95b6-4fae-b01a-eb010bc36933/image.png)
모든 요청은 Dispatcher Servlet 으로 받습니다.

```web.xml
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

Dispatcher Servlet 역시 서블릿이기 때문에 web.xml 파일에 등록을 해줘야 합니다. web.xml 파일에 서블릿을 등록하면 서블릿 설정파일이 생성됩니다.
> 설정 파일 : WEB-INF/servlet이름-servlet.xml

### WEB-INF/servlet이름-servlet.xml
- **매핑 방법 #1 (기본)** : BeanNameHandlerMapping

```xml
<beans>
    <bean name="/video" class="org.mobileCloud.controller.VideoController">
        ...
    </bean>
</beans>
```

- **매핑 방법 #2 (가장 많이 쓰는)** : DefaultAnnotationHandlerMapping

```java
@Controller
public class VideoController {
   
    @RequestMapping("/video")
    public String do() {
        // 처리 로직 
    }
}
```

### 요청에 따라 적절한 Controller 찾기
![](https://velog.velcdn.com/images/yelosta/post/50c3db67-b409-4adc-a7d9-607761aac8c8/image.png)
등록이 완료되었다면 찾아서 사용할 수도 있어야겠죠?
받은 요청을 HandlerMapping을 통해서 확인하고 적절한 Controller를 찾습니다. 찾는 방법은 Spring framework에서 제공해주며, 여기서 자세히는 다루지 않겠습니다.

### Handler(Controller) 메소드 호출
![](https://velog.velcdn.com/images/yelosta/post/4c23bed4-e5d8-456d-8000-d6c75b6a8e8d/image.png)
HandlerMapping에서 Controller를 찾았었죠? 찾은 Controller의 메소드를 호출해서 결과를 model and view 형태로 바꿔줍니다.
- model : Controller의 처리 결과
- view : 결과를 받는 페이지 <- Controller가 String 타입으로 준다.
![](https://velog.velcdn.com/images/yelosta/post/cff72f78-1d4e-40dc-8b4e-6dfbaddc7fc2/image.png)

String 타입으로 준 이름을 가지고 View Resolver가 실제 view를 찾아줍니다. 
![](https://velog.velcdn.com/images/yelosta/post/2ab91340-77b7-4c86-aacc-0b00af32b067/image.png)

그리고 나서 Controller에서 받은 model을 View에 심어 다시 view를 Dispatcher Servlet으로 돌려줍니다.

![](https://velog.velcdn.com/images/yelosta/post/f9b7b9a8-c810-4fd6-9991-292adc719ab7/image.png)
마지막으로 클라이언트에 응답을 보내게 됩니다.

# 정리
![](https://velog.velcdn.com/images/yelosta/post/bda0f9c8-85d0-40fa-ae59-51e08508d1dd/image.png)
이전에는 각 url 마다 servlet이 필요했고, Servlet에서 view를 직접 만들거나 jsp를 통해 데이터를 보낼 수 있었습니다. view과 완벽히 분리가 되지 않았던거죠.

하지만 Spring Web MVC를 사용하면서 url마다 Servlet을 만들 필요없이 모든 요청을 Dispatcher Servlet을 통해 받을 수 있고 view를 강제로 분리할 수가 있게 됩니다.
