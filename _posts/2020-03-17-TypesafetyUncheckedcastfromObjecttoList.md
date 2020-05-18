---
title: "Type safety: Unchecked cast from Object to List<>"
date: 2020-03-17
categories: posts
tags: ["Java"]
---

List<String> list = (List<String>) otherList; 에서

<b>Type safety: Unchecked cast from Object to List<></b>

경고가 발생하였다.

변수의 유형을 확실하게 하지 못해 발생하는 경고이다.

Java 는 일반적으로 컴파일을 할때 변수의 유형을 알고있어야 한다.

그러므로 불확실한 유형의 변환은 경고를 발생시킨다.

이를 해결하는 방법은 임시로 해결하는 방법과 조금은 더 근본적으로 해결하는 방법이 있다.

임시적으로 해결하는 방법은 다음과 같다.

```java
@SuppressWarnings() // 컴파일러가 일반적으로 경고하는 내용을 제외할 때 사용
```

하지만 이는 어디까지나 임시방편일뿐 이를 해결하기 위해서 명확한 유형을 정해주어야한다.

```java
List<?> result = (List<?>) results.values;
for (Object object : result) {
    if (object instanceof MyObject) {
        tempList.add((MyObject) object);
    }
}
```