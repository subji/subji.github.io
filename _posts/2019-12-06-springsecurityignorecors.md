---
title: "Spring Security Cross Domain"
date: 2019-12-06
categories: posts
tags: ["SpringSecurity"]
---
프로젝트를 하다보면 항상 마주하는 Cross Domain 문제

이번에도 어김없이 문제가 생겼다.

같은 서버에서 포트가 다른 두개의 웹서버가 있고,
한 웹 서버에서 다른 웹 서버가 관리하는 이미지를 불러올때 발생하는 문제였다.

항상 발생하는데 적어두질 않으니 매번 찾아보기를 반복한다.

그래서 찾으면서 해결? 조치 방법을 기록해놓았다.

**1. CORS 는 HTTP Header 의 OPTIONS 요청은 무시한다?**
  </br>HTTPMethod.OPTIONS 를 허용해보았다.
  </br>결과는..
  </br>아무 상관이 없었다..

**2. CorsFilter 를 추가?**
  </br>인증과정을 거치기전에 Filter 를 추가해서 사전에 CORS 검사를 해보았다.
  </br>이쯤에서 해결되면 다행이다.
  </br>하지만...
  </br>역시나 안된다. 무엇이 문제일까..

**3. 같은 도메인 혹은 같은 아이피에서 쿠키를 공유함에 있어서 생기는 문제?**
  </br>이번에 처음 겪게된 경우다.
  </br>스택오버플로우에서 검색하고 찾아봐서 이리저리 해봐도 안되던게 단지 서버에서 관리되는 쿠키 때문이었다.
  </br>그래서 curl 요청 시 쿠키를 제거하고 요청하니 CORS 에러가 발생하지 않는것이었다!

**해결 / 조치 :**
  </br>어렵지 않았다. 단지 이미지를 제공해주는 웹서버에서 세션관리를 STATELESS 하게 하면된다.
  
**소스 코드 :**
```java

/**
* Web Security Config
*/

@Override
public void configure (WebSecurity web) throw Exception {
  web.ignore().antMatcher("${URL_PATH}");
}

@Override
protected void configure (HttpSecurity http) throw Exception {
  http
      .csrf().disable()
    .and()
      .sessionManagement()
      .sessionCreationPolicy(SessionCreationPolicy.STATELESS); // 중요!
}

/**
* Web MVC Config (CORS 등록)
*/
@Override
public void addCorsRegistry (CorsRegistry registry) {
  registry
    .addMapping("/*")
    .allowedOrigins("*")
    .allowedMethods("GET", "POST", "OPTIONS", "PUT")
    .allowedHeaders("Content-Type", "X-Requested-With", "accept", "Origin","Access-Control-Request-Method", "Access-Control-Request-Headers")
    .exposedHeaders("Access-Control-Allow-Origin", "Access-Control-Allow-Credentials")
    .allowCredentials(true)
    .maxAge(3600);
}

```