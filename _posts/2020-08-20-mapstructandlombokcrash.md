---
title: "MapStruct Dependency 추가 시 빌드 오류 문제"
date: 2020-08-20
layout: single
classes: wide
categories: posts
tags: ["lombok", "mapstruct", "maven"]
---

### **Problem**
---
MapStruct 를 사용하기 위해 의존성을 추가하고 mvn clean install 을 실행하는데, "cannot find symbol" 오류를 마구 뱉어냈다.

<br>

## **How to solve?**
---
먼저 MapStruct 를 사용하기 위해서는 pom.xml 에 다음과 같이 추가해줘야한다.

```xml
<properties>
  <org.mapstruct.version>1.3.1.Final</org.mapstruct.version>
  <org.projectlombok.version>1.18.12</org.projectlombok.version>
</properties>

<dependencies>
  <dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>${org.mapstruct.version}</version>
  </dependency>
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${org.projectlombok.version}</version>
    <scope>provided</scope>
  </dependency>
  <!-- ... -->
</dependencies>

<plugins>
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
      <!-- mapstruct 를 사용할 때 lombok 과 충돌이 발생한다. 그래서 lombok 에 대한 path 도 추가해준다. -->
      <annotationProcessorPaths>
        <path>
          <groupId>org.mapstruct</groupId>
          <artifactId>mapstruct-processor</artifactId>
          <version>${org.mapstruct.version}</version>
        </path>
      </annotationProcessorPaths>
    </configuration>
  </plugin>
</plugins>
```

여기서 빌드를 하면 앞서 말한 오류가 마구 발생한다. 원인을 찾아보니 누군가가 친절하게 설명해줬다. 답은 간단하다. mapstruct-processor dependency 가 조금은 다른 방식으로 호출 되기에 annotaionProcessorPaths 에 작성하게 된다. 이떄, 만약 lombok 을 같이 사용하고 있다면, 충돌이 발생한다고 한다. 정확한 이유는 더 찾아봐야겠다. 

방법은 두가지다.

1. lombok dependency 도 annotationProcessorPaths 에 추가해준다.
2. scope 를 provided 로 작성하는 방법이다. 

[Lombok/Mapstruct problem: Cannot find symbol](https://github.com/mapstruct/mapstruct/issues/1270)

이를 토대로 다시 pom.xml 을 작성해보자. 아래는 1번 방법을 적용한 코드이다.

```xml
<properties>
  <org.mapstruct.version>1.3.1.Final</org.mapstruct.version>
  <org.projectlombok.version>1.18.12</org.projectlombok.version>
</properties>

<dependencies>
  <dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>${org.mapstruct.version}</version>
  </dependency>
  <dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>${org.projectlombok.version}</version>
    <scope>provided</scope>
  </dependency>
  <!-- ... -->
</dependencies>

<plugins>
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
      <!-- mapstruct 를 사용할 때 lombok 과 충돌이 발생한다. 그래서 lombok 에 대한 path 도 추가해준다. -->
      <annotationProcessorPaths>
        <path>
          <groupId>org.mapstruct</groupId>
          <artifactId>mapstruct-processor</artifactId>
          <version>${org.mapstruct.version}</version>
        </path>
        <path>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
          <version>${org.projectlombok.version}</version>
        </path>
      </annotationProcessorPaths>
    </configuration>
  </plugin>
</plugins>
```
더 알아보니 mapstruct 공식 github 에도 참고할 만한 코드가 몇개 올라와있었다. 

<br>

## **참고**
---

[mapstruct/mapstruct-examples](https://github.com/mapstruct/mapstruct-examples/tree/master/mapstruct-lombok)