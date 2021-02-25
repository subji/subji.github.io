---
title: "이클립스 프로젝트를 VSCODE 에 Import 할 때 이슈"
date: 2020-09-06
layout: single
classes: wide
categories: posts
tags: ["vscode", "eclipse"]
---

### **Problem**
---
이클립스에서 만든 스프링 부트 프로젝트를 vscode 에서 사용할때 classpath 를 못찾는다는 오류가 나와 실행이 안된다.

<br>

## **How to solve?**
---
```sh
~ mvn eclipse:eclipse 
```

위에 명령어를 사용하면 이클립스 프로젝트로 변환이 되며 classpath 파일이 추가된다.

<br>

## **참고**
---

[Visual Studio Code - Java Classpath is incomplete. Only syntax errors will be reported](https://stackoverflow.com/questions/40831703/visual-studio-code-java-classpath-is-incomplete-only-syntax-errors-will-be-re)