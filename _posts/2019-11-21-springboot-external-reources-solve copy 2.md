---
title: "How to access Resources (images, etc...) on spring boot (외부에서 파일 접근 방법)"
date: 2019-11-21
categories: posts
tags: ["spring boot"]
---
스프링 부트를 도커에 올려서 사용하다가 외부에서 이미지 접근이 안된다..

로컬에서는 잘 동작하지만 클라이언트가 서버상의 경로를 몰라서 인지 404에러를 뱉어냈다.

이럴 경우 다음과 같이 디렉토리 경로를 지정해주면 된다.
```java
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

	@Value("${'파일이 있는 디렉토리 경로'}")
  private String path;

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
		registry
			.addResourceHandler(path + "/**") // url 접근 경로
			.addResourceLocations("file:" + path + "/"); // 디렉토리 경로 (반드시 file: 을 붙여주어야 한다.)
  }
}
```
이로서 클라이언트에서도 서버의 리소스에 쉽게 접근이 가능해졌다.