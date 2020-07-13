---
title: "Throttling 과 Debouncing 에 대해서"
date: 2020-07-13
categories: posts
tags: ["javascript"]
---

### **들어가며**
---
DOM 이벤트 중 스크롤에 관한 것이 있다. 스크롤을 할때마다 발생하는 이벤트 같은 것이다. 이벤트 안에 로그를 찍어보면 스크롤을 할때마다 수없이 많은 로그가 출력된다. 이렇게 될 경우 이벤트 함수 안에 ajax 호출등과 같은 많은 리소스가 소모되는 동작을 하게 된다면 페이지 성능에 영향을 미치게 된다. 그래서 대안? 이라고 할 수 있는 방법이 Throttling 과 Debouncing 이다. 

<br>

### **Throttling**
---
**Throttling** 은 이벤트 안에 있는 함수의 호출 최대 횟수를 제한하는 것이다. 예를들면 다음과 같다.

스크롤 이벤트를 두고 10초동안 스크롤 동작을 수행하였을 때 함수가 총 1000번 실행된다고 하자. 한 번의 실행이 1ms 라고 했을떄, 10초 동안 총 10000ms 의 시간이 소요된다. 이때 Throttling 을 사용한다. 총 10000ms 에서 100ms 마다 한번씩 실행을 강제하자. 그러면 총 100번의 함수가 실행된다. 10초라는 시간내에 Throttling 기법을 사용해서 1/10의 시간이 줄어든게 된것이다. 

**Throttling** 은 *"100ms 초에 한번씩 실행하십시오"* 와 같이 총 실행 시간 내에서 함수 호출의 최대 횟수를 제한하는 것이다.

<br>

## **Debouncing**
---
**Debouncing** 은 **Throttling** 과 조금은 비슷하다. Debounce 는 "그룹화" 가 가능하다. 이것이 무슨말이냐하면 Debounce 의 개념을 보면 알수 있는데, Debounce 는 N 초가 지난 후 한번 실행되는 방법이다. 즉, N 초가 지나기 전까진 함수가 실행되지 않는다. 즉, 여러번 이벤트가 발생하더라고 N 초가 지난 후의 한번. 단 한번만 실행하는 것이다. 이후 다시 타이머가 재설정되고 또다시 기다렸다가 N 초후에 한번 발생하는 것이다. 

이때 Debounce 의 특징 중 하나인 "그룹화" 를 볼 수 있다. N 초 까지 기다린 이벤트 내용들이 그룹지어져 N 초 후에 한번 실행되는 것이다. 

<br>

## **Throttling, Debounce 차이**
---
차이는 간단하다. Throttling 의 경우 N 초마다 실행되는 것. Debounce 의 경우 N 초 후 한번 실행되는 것.

<br>

## **마치며**
--- 
간략하게 여러 글들을 읽은 바탕으로 정리했다. 자세한 내용은 다음을 참고하자. 

[CSS-Tricks : The difference between throttling and debouncing](https://css-tricks.com/the-difference-between-throttling-and-debouncing/)