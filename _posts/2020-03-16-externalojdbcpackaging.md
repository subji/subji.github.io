---
title: "외부 Jar 추가 방법"
date: 2020-03-16
layout: single
classes: wide
categories: posts
tags: ["maven"]
---

스프링 부트를 패키징하고 배포하는 과정에서 외부 ojdbc 를 사용해야 하는데 

배포후에 적용이 안되서 실행이 안됬다.

로컬에서는 mvn install:install-file 을 사용해서 로컬 PC 의 리포지토리에서

사용했는데 배포한 서버의 로컬 리포지토리에는 ojdbc 가 없었다..

또한 패키징 과정에서 ojdbc 파일도 누락되었다.

두가지 방법이 있는데, 하나는 서버의 로컬 리포지토리에 파일을 설치하는 방법

다른 하나는 패키징할때 프로젝트 디렉토리안에 자체 리포지토리를 만드는 것이다.

프로젝트 리포지토리를 만드는 방법은 간단하다.

다음과 같이 lib 아래에 외부 jar 파일을 옮겨놓는다.

<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/externalojdbcpackaging_1.PNG">
</div>

그리고 pom.xml 에 만들어놓은 디렉토리를 리포지토리로 지정한다.

```xml
  <repositories>
    <repository>
        <id>local-repository</id>
        <name>local repository</name>
        <url>file://${project.basedir}/lib</url>
    </repository>
  </repositories>
```

마지막으로 외부 ojdbc 에서 선언한 아래와 같은 내용을 주석처리하고 다시 패키징한다.

```xml
  <scope>system</scope>
	<systemPath>${basedir}/src/lib/ojdbc7.jar</systemPath>
```