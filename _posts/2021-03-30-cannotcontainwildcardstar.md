---
title: "java.lang.IllegalArgumentException: 'Content-Type' cannot contain wildcard type '*'"
layout: single
classes: wide
date: 2021-03-30
categories: posts
tags: ["springbood"]
---

# 오류
RestTemplate 으로 요청을 하는데 온전히 전송되지도 않고 오류를 뱉어냈다. 오류 내용을 그대로 보면 "Content-Type" 에는 "*" 와 같은 와일드 카드를 포함할 수 없다고 나온다. 그래서 코드를 살펴보았는데,

```java
HttpHeaders headers = new HttpHeaders();
HttpEntity<Object> entity = new HttpEntity<>(params, headers);
```

라고 되어있었다. 다만 궁금한 점은 아래 코드 부분인데,

```java
public void setContentType(@Nullable MediaType mediaType) {
  if (mediaType != null) {
    Assert.isTrue(!mediaType.isWildcardType(), "Content-Type cannot contain wildcard type '*'");
    Assert.isTrue(!mediaType.isWildcardSubtype(), "Content-Type cannot contain wildcard subtype '*'");
    set(CONTENT_TYPE, mediaType.toString());
  }
  else {
    remove(CONTENT_TYPE);
  }
}
```
Content-Type 을 설정하지 않았을 경우에 또는 알 수 없는 경우에 "*" 와 같은 와일드카드가 설정되는가이다. 

찾아보니 Accept 헤더를 설정하지 않거나 사용하지 않는 경우에 혹은 MediaType.ALL 로 설정한 경우에는 응답 타입을 알지 못한다. 그래서 제목과 같은 오류를 발생한다고 한다.

오류 해결 방법은 간단했다. Header 에 Content-Type 을 지정해주면 된다.

```java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);
```