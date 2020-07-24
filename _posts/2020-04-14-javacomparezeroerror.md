---
title: "Comparison method violates its general contract! 에러"
date: 2020-04-14
categories: posts
tags: ["Java"]
---

간혹 정렬을 하고자 할때 a > b ? 1 : -1 과 같이 쓸때가 있다.

java 9 이전 버전에서는 위와 같이 쓰면 == 에 해당하는 경우가 명시되어있지 않기때문에 오류가 발생한다.

방법은 여러가지가 있다.

첫째는 자바 버전을 올리는 것이다.

하지만 버전을 현업에서는 내가 맘대로 올릴수도 없으니..

둘째는 조금 코드량이 많아지지만 다음과 같이 작성하는 방법이 있다.

```java
  if (a > b)  {
    return 1;
  } else if (a < b) {
    return -1;
  } else if (a == b)  {
    return 0;
  }
```

한 두곳이나 저렇게 쓸 수 있지 여러곳이면 손가락 아플거 같다.

마지막은 조금 더 간단하게 작성하는 방법이다.

```java
  return Long.compare(a, b);
```

래핑 객체를 비교하면 더 낫다.