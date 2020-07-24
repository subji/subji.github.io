---
title: "Spring Getter & Setter 보안취약성 관련"
date: 2019-12-20
categories: posts
tags: ["Spring Framework"]
---
웹 어플리케이션의 보안취약성 점검을 받고

다음과 같은 문제가 있다는것을 보고받았다.

"Public 메소드부터 반환된 Private 배열 - private로 선언된 배열이 public 메소드를 통해 반환이 되고 있음"

알고보니 Getter & Setter 가 정의된 VO 혹은 Domain 파일이었다.

다른 곳은 아무 이상없는데 왜 저기 특정 메소드에만 문제가 있는것인가 하는 의문이 생겼다.

자세히 보니 다른 메소드에서는 기본형 타입을 반환하고 있었고, 문제가 된 해당 메소드에서는 참조(객체) 타입을 

반환하고 있었다.

즉, 기본형 타입 (int, String, .. 등) 이 아닌 참조(객체) (Array, ... 등) 은 주소 정보가 전달되기 때문에

이를 그대로 반환하는 것보단 새로 복사를 해서 반환해야 한다는 것이었다.

그래서 다음과 같이 수정했다.
  
**소스 코드 :**
```java
private String[] test;

public String[] setTest (String[] test) {
  String[] temp = new String[test.length];
  System.arraycopy(test, 0, temp, 0, test.length);
  this.test = temp;
}

public String[] getTest (String[] test) {
  String[] temp = new String[test.length];
  System.arraycopy(test, 0, temp, 0, test.length);
  return temp;
}
```