---
title: "로그인 다시 안하고 사용자 권한 업데이트 & 변경하기"
date: 2020-09-03
categories: posts
tags: ["spring security", "spring"]
---

### **Problem**
---
회원가입, 멤버십 구매등의 로직을 개발하면서 구매 완료시점에서 사용자의 권한을 변경 또는 추가를 해줘야했다. 단순한 방법으로는 가입 및 구매 완료 후 다시 로그인 화면으로 옮기는 방법이 있다.

하지만 이 방법은 사용자로 하여금 불편한? 과정일 것이다. 그래서 찾아보니 로그아웃 - 로그인 없이 Spring 의 Security 에 있는 SecurityContext 의 값을 변경해주면 되는 방법이 있었다.

다음은 간단한 사용방법의 예이다.

<br>

## **How to solve?**
---
```java
Authentication auth = SecurityContextHolder.getContext().getAuthentication();

List<GrantedAuthority> updatedAuthorities = new ArrayList<>(auth.getAuthorities());
updatedAuthorities.add(...); //add your role here [e.g., new SimpleGrantedAuthority("ROLE_NEW_ROLE")]

Authentication newAuth = new UsernamePasswordAuthenticationToken(auth.getPrincipal(), auth.getCredentials(), updatedAuthorities);

SecurityContextHolder.getContext().setAuthentication(newAuth);
```

나는 Keycloak 을 사용하고 있어서 Keycloak 버전도 올려본다.

```java
  List<GrantedAuthority> newRoles = new ArrayList<>();

  if (result.getStatusCode().is2xxSuccessful()) {
    newRoles = result.getBody().stream().map(
      res -> {
        // {keycloak auth url}/auth/admin/realms/{realm name}/users/{user id(uuid)}/role-mappings/clients/{clientid{uuid}, containerid};
        // 요청 후 받아온 값에서 Security 의 기본 접두사인 ROLE_ 이 생략되어 있으므로 추가해주어야 한다. 
        // 추가하지 않으면 Security 에서 원할한 검사가 되지 않는다.
        String role = "ROLE_" + ((String) res.get("name"));
        return new KeycloakRole(role);
      }
    )
    .collect(Collectors.toList());
  }

  Authentication auth = SecurityContextHolder.getContext().getAuthentication();
  KeycloakAuthenticationToken existToken = (KeycloakAuthenticationToken) auth;

  List<GrantedAuthority> oldRoles = auth.getAuthorities().stream().map(authority -> new KeycloakRole(authority.getAuthority())).collect(Collectors.toList());
  newRoles.addAll(oldRoles);

  Authentication newAuth = new KeycloakAuthenticationToken(existToken.getAccount(), existToken.isInteractive(), newRoles);
  SecurityContextHolder.getContext().setAuthentication(newAuth);
```

<br>

## **참고**
---

[How to reload authorities on user update with Spring Security](https://stackoverflow.com/questions/9910252/how-to-reload-authorities-on-user-update-with-spring-security)