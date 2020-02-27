---
title: "Spring Boot 가 아닌 프로젝트에서 Actuator 사용하기 (How to use Actuator without Boot"
date: 2020-02-27
categories: posts
tags: ["spring boot", "spring"]
---

## | 문제
레거시 프로젝트들의 모니터링 시스템을 만들던중 Spring Boot 에서는 간단한 설정만으로 해당 어플리케이션의 정보를 가져오는 Actuator 를 적용시키고자 하였다.

하지만 Actuator 는 Spring Boot 프로젝트의 일환이였던것..

그럼 기존의 레거시 시스템에는 어떻게 적용을 해야하는건가..

## | 과정
구글링에서는 없는게 없다.   
라는 확신을 갖고 열심히 찾아보았다.   
먼저 pom.xml 에 actuator Dependency 를 추가하고..  
xml 설정으로는 너무 어려우니까 관리가 편한 자바 설정으로 해봐야겠네..

열심히 찾고 테스트하고 확인한 결과 다음과 같이 사용하면 된다는걸 알았다.

- POM.xml 에 의존성을 추가
- Java Configuration 을 사용해서 원하는 EndPoint 를 설정해주는 객체를 생성
- Security 외에 다른 보안 처리를 작업중이었다면 /health, /metrics 등의 접근을 허용

## | 결론
**POM.xml**

```xml
<!-- actuator -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-actuator</artifactId>
  <version>1.2.1.RELEASE</version>
</dependency>
```
**Java**

```java
@Configuration
@Import({
	EndpointAutoConfiguration.class,  // Endpoint 자동 설정 객체
	PublicMetricsAutoConfiguration.class, // Metrics 객체
	HealthIndicatorAutoConfiguration.class  // Health 객체
})
public class ActuatorConfig {
	
  @Bean
  @Autowired
  public EndpointHandlerMapping endpointHandlerMapping(Collection<? extends MvcEndpoint> endpoints) {
      return new EndpointHandlerMapping(endpoints);
  }

  /**
    * Metrics 접근 객체 Bean 등록
    */
  @Bean
  @Autowired
  public EndpointMvcAdapter metricsEndPoint(MetricsEndpoint delegate) {
      return new EndpointMvcAdapter(delegate);
  }
  /**
    * Health 접근 객체 Bean 등록
    */
  @Bean
  @Autowired
  public EndpointMvcAdapter healthEndPoint (HealthEndpoint delegate)	{
    return new EndpointMvcAdapter(delegate);
  }
}
```