---
title: "Spring Boot (Maven) with React Study day 3"
date: 2020-02-26
categories: posts
tags: ["Spring Boot", "Maven", "React"]
---
<br>
**목적**
===
- Spring boot 와 React 를 활용하여 간단한 웹 페이지를 만들어보고자 한다.

## **이전 내용**
이전에는 스프링부트의 설치하는 방법과 실행방법에 대해서 살펴보았다.
<br>
[Spring Boot (Maven) with React Study day 2](https://subji.github.io/posts/2020/02/14/react-with-spring-2)
<br>

이번에는 create-react-app 과 설치된 스프링 부트 앱의 빌드 및 배포 연동을 해보도록 하겠다.

## **1. 연동 방법**
---
스프링부트의 static 디렉토리 안에 .html 파일이 있다.   
해당 파일을 리액트의 파일로 대체해주는 방법이 있다.   
즉, SPA 인 리액트를 빌드해서 만들어진 js, css, html 파일들을 스프링부트의 정적 리소스 디렉토리에 복사해주는 것이다.   
그러면 스프링부트에서는 classpath 를 훑으며 자연스레 리액트 파일을 실행할 것이다.

### **2. 구글링**
---
내가 찾은 방법으로는 대략 2가지 정도가 있었다.   
**첫번째**는 webpack 으로 만들어진 리액트 프로젝트를 스프링부트 프로젝트 내 별도의 디렉토리에 두고    
Maven 또는 Gradle 에서 스프링부트의 빌드와 동시에 리액트 앱도 빌드해서 완료된 파일들을 복사해주는 방법이 있다.   
**두번쨰**는 Maven 의 경우인데, frontend-maven-plugin, maven-antrun-plugin 을 활용하는 방법이다.   
여기서는 Maven 으로 프로젝트를 생성했으므로 두번째 방법을 사용해보도록 하자.   
다음 프로젝트에서는 꼭 Gradle 도 사용해보자.

## **3. 스프링부트 & 리액트 빌드**
--- 
**frontend-maven-plugin Version** : [Recently Version](https://github.com/eirslett/frontend-maven-plugin) 링크를 통해 해당 메이븐 플러그인의 최신버전을 사용하거나 원하는 버전을 사용하자.
```xml
<plugin>
    <groupId>com.github.eirslett</groupId>
    <artifactId>frontend-maven-plugin</artifactId>
    <version>1.7.6</version> <!-- frontend-maven-plugin Version -->
    <configuration>
        <workingDirectory>frontend</workingDirectory> <!-- React JS 가 설치된 디렉토리 -->
        <installDirectory>target</installDirectory> <!-- war & jar 가 생성되는 메이븐 빌드의 타겟 디렉토리 -->
    </configuration>
    <executions>
        <execution>
            <id>install node and npm</id>
            <goals>
                <goal>install-node-and-npm</goal> <!-- node & npm 설치 -->
            </goals>
            <configuration>
                <nodeVersion>v12.16.1</nodeVersion> <!-- 설치할 nodeJs 의 버전 -->
                <npmVersion>6.14.0</npmVersion>  <!-- 설치할 npm 의 버전 -->
            </configuration>
        </execution>
        <execution>
            <id>npm install</id>    <!-- npm install 로 package.json 의 모듈을 설치한다. -->
            <goals>
                <goal>npm</goal>
            </goals>
            <configuration>
                <arguments>install</arguments>
            </configuration>
        </execution>
        <execution>
            <id>npm run build</id>  <!-- React Js Build -->
            <goals>
                <goal>npm</goal>
            </goals>
            <configuration>
                <arguments>run build</arguments>
            </configuration>
        </execution>
    </executions>
</plugin>
```
위에 코드를 작성하고 터미널 또는 cmd 를 열고 "mvn clean install" 을 해보자.   
아래 그림과 같은 진행사항이 표시된다.   

그리고 나서 리액트가 설치된 디렉토리를 보면 "build" 라는 디렉토리가 생기고 그안에 js, css, html 이 생성되었을 것이다.

<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202002261039-springwithreact.png">
</div>

<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202002261041-springwithreact.png">
</div>

이렇게 되면 이제 마지막 단계만 남았다.


아래 코드를 pom.xml 에 추가해주자.

```xml
<plugin>
  <artifactId>maven-antrun-plugin</artifactId>
  <executions>
    <execution>
      <phase>generate-resources</phase> <!-- 리소스 생성 -->
      <configuration>
        <target>
          <copy todir="${project.build.directory}/classes/public">  <!-- 복사할 디렉토리 설정 -->
            <fileset dir="${project.basedir}/frontend/build"/>  <!-- 가져올 디렉토리 및 파일 -->
          </copy>
        </target>
      </configuration>
      <goals>
        <goal>run</goal>  <!-- 복사 실행 -->
      </goals>
    </execution>
  </executions>
</plugin>
```
위에 코드를 작성하고 다시 "mvn clean install" 을 실행해보자.

그러면 다음과 같이 target 디렉토리 아래에 생성될 것이다.

<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202002261046-springwithreact.png">
</div>

이제 모든게 다 되었다.

target 아래에 생성된 .war 파일을 "java -jar *.war" 로 실행하자.

```sh
java -jar *.war
```

이제 실행된 주소로 접속해보면 다음 화면이 나온다!

<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202002261049-springwithreact.png">
</div>

## **정리**
---
1. Spring Boot 와 React 연동 방법
2. React 를 빌드해서 Spring Boot Target 디렉토리에 복사

## **마무리**
간단하게 리액트와 스프링부트를 연동해보았다.

이를 사용해서 다음에는 리액트를 개발하고 스프링부트는 API 로 활용하여 빠른개발에 도움이 될거 같다.