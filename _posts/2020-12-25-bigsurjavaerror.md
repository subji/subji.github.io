---
title: "MacOS BigSur 에서 Java 환경변수 못찾는 오류"
date: 2020-12-25
categories: posts
tags: ["java", "mac"]
---

### **문제**
---
BigSur 로 업데이트를 하고나서 VSCode 에서 스프링 개발좀 하려고 하는데 스프링 부트 대시보드가 보이지 않고 자바 커맨드도 찾을 수 없다는 오류가 나왔다.   
<br>
<br>

### **해결**
---
BigSur 로 업데이트되고 나서 바뀐 자바 환경 때문에 발생하는 오류였다. BigSur 에서는 기본적으로 /usr/bin/javac 를 자바 기본 환경변수로 정해져있다. 즉, jdk 경로를 심볼릭 링크로 정해놓지 않는다. 그래서 java 커맨드를 사용할때 올바른 동작을 할 수 없었다.

<br>

먼저 현재 설치된 jdk 의 경로를 보기위해 아래 커맨드를 입력한다. 그러면 설치된 jdk 의 경로가 나온다.
```bash
/usr/libexec/java_home -V

Matching Java Virtual Machines (1):
    15.0.1 (x86_64) "AdoptOpenJDK" - "AdoptOpenJDK 15" /Library/Java/JavaVirtualMachines/adoptopenjdk-15.jdk/Contents/Home
/Library/Java/JavaVirtualMachines/adoptopenjdk-15.jdk/Contents/Home # jdk 경로
```
<br>

jdk 경로를 확인했다면 이제 JAVA_HOME 을 설정해서 /usr/bin/javac 의 환경변수를 연결해준다. Mac 은 zsh 가 기본 쉘로 설정되어있기 때문에 .zshrc 파일을 열어서 다음 값을 입력하고 저장한다.
```bash
vi ~/.zshrc
```
```bash
# .zshrc file

JAVA_HOME=`/usr/libexec/java_home`
```
```bash
source ~/.zshrc
```
<br>

마지막으로 JAVA_HOME 이 잘 설정되었는지 확인한다. 결과가 jdk 경로로 나온다면 성공이다.
```bash
echo $JAVA_HOME

/Library/Java/JavaVirtualMachines/adoptopenjdk-15.jdk/Contents/Home
```
<br>

### **참고**
---
[Error in every java file after updating to macOS Big Sur](https://github.com/redhat-developer/vscode-java/issues/1700#issuecomment-729482878)