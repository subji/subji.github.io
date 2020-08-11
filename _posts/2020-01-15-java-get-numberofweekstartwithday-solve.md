---
title: "Java 특정 요일을 기준으로 주차 구하기(Get number of week where first day of some day)"
date: 2020-01-15
categories: posts
tags: ["java"]
---

## | 문제
년도를 기준으로 주차를 구하거나 달의 주차를 구할때<br/>Calendar 객체에 기본값으로 정해진 주차를 구하다보니
원하는 주차가 나오지 않는 경우가 발생하였다.

## | 해결
- Calendar 의 인스턴스를 가져올때 파라미터로 로케일값을 주는 방법이있다.
```java
  // 예제 - 
  Calendar cal = Calendar.getInstance(new Locale("en", "US")); // 일요일 기준 주차 구하기
  Calendar cal = Calendar.getInstance(new Locale("en", "UK")); // 월요일 기준 주차 구하기
  cal.get(Calendar.WEEK_OF_YEAR);
```
- Calendar 객체의 setFirstDayOfWeek, minimalDaysInFirstWeek 를 적용하는 방법이있다.<br/>
minimalDaysInFirstWeek 메소드는 해당 연도의 첫주에 포함할 최소 일 수를 설정하는 것이다.<br/>
setFirstDayOfWeek 에 기준요일을 설정하고, minimalDaysInFirstWeek 에 포함할 일 수를 설정하면된다.
```java
  // 예제 - 일요일을 시작요일로 설정하고 주차를 계산
  Calendar cal = Calendar.getInstance();
  cal.setFirstDayOfWeek(Calendar.SUNDAY);
  cal.minimalDaysInFirstWeek(7);
  cal.get(Calendar.WEEK_OF_YEAR);
```