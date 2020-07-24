---
title: "Blocking & Non-Blocking, Syncronous & Asyncronous"
date: 2020-06-03
categories: posts
tags: ["Javascript"]
---
### <b>Blocking & Non-Blocking 과 Syncronous & Asyncronous 를 간단히 알아보고 차이점을 확인해보자.</b>
--- 
<b>Blocking</b> 은 어떤 요청을 클라이언트가 주었을때, 서버에서 완료 혹은 오류 처리를 반환하기 전까지 클라이언트가 다른 호출을 할 수 없이 대기하는 상태이다.

<b>Non-Blocking</b> 은 어떤 요청을 클라이언트가 주었을때, 서버에서 완료 혹은 오류와 상관없이 클라이언트에 다른 호출을 할 수 있게 해주는 것이다.

<b>Syncronous</b> 는 C, Java 와 같은 프로그래밍에서 볼 수 있는 순차적 프로그래밍 방식이라고 생각하면 쉽다. 처음 호출된 함수 또는 코드가 완료되기 전까지 다음 함수 또는 코드가 실행되지 않는 방식이다.

<b>Asyncronous</b> 는 Syncronous 와 달리 함수 또는 코드의 완료 여부와 상관없이 다음 함수 또는 코드가 실행되는 방식이다.