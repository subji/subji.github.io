---
title: "Spring Boot JUnit 에서 한글 깨지는 오류"
layout: single
classes: wide
date: 2021-02-04
categories: posts
tags: ["spring boot", "junit", "test"]
---

# JUnit 에서 응답 값의 결과 중 한글이 깨지는 오류
JUnit 으로 mockmvc.perform 으로 응답값을 가져와서 확인하려던 중 결과에 한글이 포함되었을 때 깨지는 오류가 발생하는 것을 확인하였다. 

찾아보니 Spring 5.2 버전 이후 Spring Boot 의 경우 2.2 버전 이후 부터 기본 인코딩 방식으로 UTF-8 을 더이상 지원하지 않는다고 한다. 이는 기본값이 ISO-8859-1 로 변경되었기 때문이라고 한다. 

</br>
</br>
</br>

# 해결방법
@before 의 setup 함수에서 UTF-8 로 인코딩을 해주는 필터를 추가해주면된다.
```java
@Before
public void setUp() {
	this.mockMvc = MockMvcBuilders
		.webAppContextSetup(this.context)
		.addFilter(((request, response, chain) -> {
			response.setCharacterEncoding("UTF-8");
			chain.doFilter(request, response);
		}))
		.apply(springSecurity()).build();
}
```


# 참고
- [Spring Boot MVC Test - MockMvc is always null](https://stackoverflow.com/questions/59534247/spring-boot-mvc-test-mockmvc-is-always-null)
- [MockMvc no longer handles UTF-8 characters](https://github.com/spring-projects/spring-framework/issues/23219)