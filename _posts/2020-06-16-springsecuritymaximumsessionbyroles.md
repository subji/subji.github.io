---
title: "Spring Security 권한별 Maximum Sessions 결정 방법"
date: 2020-06-16
layout: single
classes: wide
categories: posts
tags: ["spring security"]
--- 

스프링 시큐리티를 사용하다보면 중복 로그인을 막거나 해제해야되는 경우가 있습니다.

이경우에는 일반적으로 maximumSessions() 와 maxSessionPreventsLogin() 을 사용하곤 합니다. 그런데 조금 더 상세하게 설정을 하고 싶을떄가 있습니다. 예를들어 관리자의 경우에는 중복로그인을 허용하고 그외 사용자의 경우에는 중복로그인을 막는 것과 같이 말이죠.

그런데 위 설정만으로는 전체적으로 중복로그인을 막는다거나 혹은 특정 세션수만큼 풀어준다거나 그것도 아니면 아예 중복로그인을 허용한다거나 하는 것 밖에는 없어보입니다.

그래도 혹시 다른 무언가 방법이 있지 않을까해서 찾아보니 "ConcurrentSessionControlAuthenticationStrategy" 라는 클래스를 사용하면 된다는 군요. 스프링 시큐리티의 기본 Maximum Session 설정은 전역으로 적용되게 되어있습니다. 하지만 앞서 말한 "ConcurrentSessionControlAuthenticationStrategy" 의 "getMaximumSessionsForThisUser" 메소드를 사용하면 아주 쉽게 권한 또는 사용자의 정보에 따라서 세션수를 조절하는게 가능하다고 합니다.

더불어 세션 정책?전략? 에 대해서 사용자가 임의로 정하고 싶을때에는 "SessionAuthenticationStrategy" 의 구현체를 작성하거나 위에서 언급한 "ConcurrentSessionControlAuthenticationStrategy" 의 메소드인 "getMaximumSessionsForThisUser" 를 오버라이드하여 구현하면 되겠습니다.

아래는 getMaximumSessionsForThisUser 를 Override 하는 예제입니다.

```java
public class CustomConcurrentSessionControlAuthenticationStrategy extends ConcurrentSessionControlAuthenticationStrategy {
    // 최대 세션 수 권한별로 적용
    protected int getMaximumSessionsForThisUser(Authentication authentication) {
        boolean admin = // Check authentication.getAuthorities() for the admin role
        return admin ? 8 : 1;

    }
}
```
위에서 만든 MaximumSession 메소드의 구현체를 Bean 으로 등록합니다.
```java
@Bean
public CustomConcurrentSessionControlAuthenticationStrategy sessionControlStrategy() {
    return new CustomConcurrentSessionControlAuthenticationStrategy(new SessionRegistryImpl());
}
```
등록한 Bean 을 SessionManager 의 SessionAuthenticationStratege 에 등록합니다.
```java
sessionManagement().sessionAuthenticationStrategy(sessionControlStrategy());
```
권한별로 최대 세션 허용 수를 설정하는 법을 알아보았습니다.


출처 : [Spring Security concurrent session control per User group or per single user
](https://stackoverflow.com/questions/26761043/spring-security-concurrent-session-control-per-user-group-or-per-single-user#comment42706655_26774817)
