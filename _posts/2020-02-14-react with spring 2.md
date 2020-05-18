---
title: "Spring Boot (Maven) with React Study day 2"
date: 2020-02-14
categories: posts
tags: ["SpringBoot", "Maven", "React"]
---
<br>
**목적**
===
- Spring boot 와 React 를 활용하여 간단한 웹 페이지를 만들어보고자 한다.

## **이전 내용**
이전에는 React 의 간단한 소개와 더불어 주요개념중에 일부인 VirtualDOM 과 JSX 를 알아보았다.   
그리고 create-react-app 명령어로 첫 react 앱을 실행시켜보았다.
<br>
[Spring Boot (Maven) with React Study day 1](https://subji.github.io/posts/2020/02/12/react-with-spring-1)
<br>

이번 시간에는 Maven 빌드툴을 기반으로한 Spring Boot 환경을 설정하고 실행을 해보자.

## **1. Spring Boot ?**
---
기존의 Spring MVC 의 경우에는 개발자가 두루두루 사용되는 라이브러리들의 설정을 직접 하나하나 해줘야했다.   
매 프로젝트마다 같은 작업을 반복하는 것은 참으로 지겹고도 고된 일이다.   
그래서 나온게 Spring Boot 다.

Spring Boot 는 간단히 말해 Java 로 어플리케이션을 빠르고 간편하게 개발할 수 있도록 도와주는 도구이다.

### **1-1. Why Spring Boot ?**
---
그렇다면 그냥 Spring MVC 를 사용해서 만들어도 되는데 굳이 Spring Boot 라는 것을 또 배워서 적용해야 될까?

기존의 Spring 개발방식을 고집한다면 말릴순 없다. 틀린건 아니니까..   

하지만 나처럼 반복되는 일을 지겨워한다면 또는 기본적인 설정정도는 알아서 해줬으면 하는 개발자라면 한번쯤 사용해볼만하다.

Spring Boot 를 사용함으로써 얻는 이점은 다음과 같다.

- Was 구축이 편리하다.
  - Spring MVC 에서는 톰캣을 다운로드해서 IDE 에 추가하고 어플리케이션과 연결도 해야했다.    
  하지만 Spring Boot 의 경우에는 내장 톰캣을 지원하여 Spring Boot 프로젝트를 생성하고 실행만하면 된다.
- 자주 사용되는 또는 다양한 종류의 라이브러리들이 지원된다. 
- 기존의 Spring 방식보다 가볍고 쉽다.
  - 기존의 Spring 에서는 xml 파일을 만들고 설정을 작성하는 어렵고도 고된 방식이였다. Spring Boot 에서는   
  이러한 복잡하고도 어려운 방식을 .properties 또는 .yml 파일에 모아 쉽고 간편하게 작성이 가능하다.

## **2. Install Spring Boot**
--- 
Spring Boot 를 설치해보고 실행시켜보자.
### **2-1. Install VSCode**
---
[VSCode 설치](https://code.visualstudio.com/) 에서 자신의 OS 에 맞는 VSCode 를 다운로드한다.

### **2-1. Install Spring Plugin**
---
Ctrl + Shift + P (Mac 의 경우 Cmd + Shift + P) 를 눌러 아래 확장프로그램을 설치한다.
- Debugger for Java
- Language Support for Java
- Java Test Runner
- Maven for Java
- Spring Boot Tools
- Spring Initializer Java
- Spring Boot DashBoard
- Spring Boot Extentions

설치가 완료되면 VSCode 의 지시에 따라 재시작을 하고 다시 Ctrl + Shift + P 를 누른다.
<div style="width: 100%;">
  <img src="https://subji.github.io/assets/images/spring-boot-initializer.PNG" style="width: 100%">
</div>
위처럼 "Spring initializer: Generate a Maven Project" 로 프로젝트를 생성한다.

처음에는 어떤 언어로 개발할 것인지를 묻는다 (Java, Kotlin, Groovy) 

나는 Java 만 알고있기 때문에 Java 를 선택하였다.

이후에는 GroupId 와 ArtifactId 를 입력하고 진행한다.

그 다음으로는 중요한 항목인데

<div style="width: 100%;">
  <img src="https://subji.github.io/assets/images/spring-boot-initializer-select-ilbrary.PNG" style="width: 100%">
</div>

위처럼 Dependency 를 선택하는 창이 나온다. 여기서 개발자가 원하는 Dependency 를 선택하여 추가할 수 있다.

정말 간편하지 않은가?

그리고 마지막으로 프로젝트를 생성할 디렉토리를 선택하면 프로젝트가 생성된다.

## **3. Run Spring Boot**
---
왼쪽 아래 "SPRING-BOOT DASHBOARD" 를 확인하면 생성한 프로젝트명이 표시된다.
<div style="width: 100%;">
  <img src="https://subji.github.io/assets/images/run-spring-boot-project.PNG" style="width: 100%">
</div>
화살표 버튼을 누르거나 프로젝트명을 우클릭하여 run/debug 모드로 실행할 수 있다.

## **정리**
---
1. Spring Boot 는 간편하다.
2. Spring Boot 프로젝트 생성 및 실행

## **다음에 정리할 내용**
---
- Spring Boot & React 연동 