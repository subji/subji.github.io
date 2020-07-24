---
title: "SPRING 에서 PROXY 된 IP 까지 받아오는 방법"
date: 2019-06-18
categories: posts
tags: ["Spring Framework"] 
---

기본적으로 HttpServletRequest.getRemoteAddr() 로 받아 올 수 있지만

Proxy 된 IP 의 경우 제대로 받아오지 못한다.

그 이유는 웹서버 또는 WAS 이전에 Load Balancers (L4, ...), Proxy Server, Caching Server, HTTP 서버용 WAS Connector 등이 있을 경우 

앞서 언급한 기타 Server 들의 경우 웹서버 & WAS 에 HTTP 또는 AJP(전용 프로토콜) 으로 요청을 보낸 후 받은 결과를 클라이언트에 재전송하게 된다.

이로 인해 "getRemoteAddr()" 은 null 을 출력한다. 그러면 어떻게 방법은 없는 걸까? 하고 찾아보니 X-Forwarded-For 등 HTTP Header 에서 받아오는 방법이 있었다.

아래는 X-Forwarded-For 의 간략한 설명과 예제 코드이다.

X-Forwarded-For (XFF) 는 HTTP Header 중 하나로 HTTP Server 에 요청한 Client IP 를 식별하기 위한 방법 중 하나이다.

일반적으로 Java 에서 Client IP 를 받아오기 위해선 아래와 같은 방법으로 작성한다.

```java
HttpServletRequest.getHeader("X-Forwarded-For");
HttpServletRequest.getHeader("Proxy-Client-IP");
HttpServletRequest.getHeader("WL-Proxy-Client-IP");
HttpServletRequest.getHeader("HTTP_CLIENT_IP");
HttpServletRequest.getHeader("HTTP_X_FORWARDED_FOR");
```

위 코드를 사용해도 로컬 환경에서는 "0:0:0:0:0:0:0:1" 과 같이 IPv6 로 출력된다. 해당 값은 IPv4 에서 "127.0.0.1" 과 같은 값이다.

Tomcat 의 경우 "{"tomcat directory"}/bin/catalina.bat" 파일에 다음과 같이 작성한다.

```java
:noJuliConfig
set "JAVA_OPTS=%JAVA_OPTS% %LOGGING_CONFIG%" -Djava.net.preferIPv4Stack=true
:noJuliManager
set "JAVA_OPTS=%JAVA_OPTS% %LOGGING_MANAGER%" -Djava.net.preferIPv4Stack=true
```
