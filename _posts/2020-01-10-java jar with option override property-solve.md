---
title: "자바 & 스프링 실행 명렁어에서 속성값을 변경하는 실행 커맨드(how to change property when use java -jar command with option)"
date: 2020-01-10
categories: posts
tags: ["java", "spring"]
---

## | 문제
스프링 부트 프로젝트를 하면서 application.yml 내의 프로퍼티값을 변경 & 추가해주어야 하는 상황이 발생했다.

## | 해결
보통 war 파일을 실행할 때 java -jar file.war 로 실행한다.

이때, -Drun.arguments=--{프로퍼티명=값} 을 추가해주면 application.yml 내의 값이 변경 & 추가가 된다.

처음에는 -D프로퍼티명=값 으로 했는데 적용이 안되었다.

그래서 찾아보니 -D 는 시스템 프로퍼티를 설정하는 옵션이라고 한다.

그래서 시스템 프로퍼티인 run.arguments 를 사용하여 커스텀 프로퍼티를 넘겨주니 잘 된다.

주의할 점은 스프링부트의 메인 함수에서 다음과 같이 작성이 되어있어야 한다.
  
## | 소스 코드
```shell
java -jar -Drun.arguments=--test.test=test application.war
```
```java
public static void main(String[] args) {
  // args 를 반드시 파라미터로 넘겨주어야 한다.
  SpringApplication.run(MainApplication).class, args);
}
```