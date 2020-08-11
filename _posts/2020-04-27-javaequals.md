---
title: "Java Equals '==' 차이"
date: 2020-04-27
categories: posts
tags: ["java"]
---

보통 String 객체의 경우 equals 를 사용하고

int 변수의 경우 == 을 사용한다.

이유는 무엇일까?

그 차이는 객체와 값에 있다.

전자인 String 객체 비교의 경우 값이 아닌 객체를 비교한다.

객체를 비교한다는 것은 Call By Reference 로 참조값을 비교하는 것이다.

후자인 int 의 경우 Wrapping 객체가 아닌 Primitive 타입이므로 Call By Value 로 값을 비교하게 된다.

예를들어

```java
  String a = "aaa";
  String b = a;
  String c = "aaa";

  a == b // True
  b == c // False
```

위에서 보면 분명 a ("aaa") 의 값을 b 로 복사해주었으니 당연히 c ("aaa") 와 같을거라 생각할수 있다.

하지만 다르다.

a 와 c 의 값은 같다. 하지만 그 값을 가지고 있는 객체가 다르다.

다만 a 객체를 b 객체로 복사해주었으므로 a 와 b 는 같은 객체이다.

String 은 Primitive 타입이 아닌 객체 이므로 a 객체의 값 ("aaa") 를 비교하는 것이 아닌 그 값을 가진

객체를 비교하는 것이다.

이때 비교하게 되는게 주소값인데, 객체는 그 각자가 서로 다른 주소값을 가지게 된다.

그러므로 a 를 할당받은 b 는 a 와 같은 주소값을 가지므로 "==" 연산자로 비교할떄 주소값으로 비교해 같게 되는것이다.

그래서 객체의 값을 비교하는 메소드인 equals 를 써야 정확하게 객체를 비교할 수 있다.

다음으로 int 형의 경우 Integer 라는 Wrapping 객체가 있지만 int 형 자체는 원시 타입 (Primitive Type) 이기에 단순히 값을 비교할 수 있는 것이다.