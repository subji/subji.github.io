---
title: "Gradle : Could not initialize class org.codehaus.groovy.runtime.InvokerHelper Error"
date: 2020-12-28
categories: posts
tags: ["gradle"]
---

### **문제**
---
Gradle Test 를 하는 과정에서 **Could not initialize class org.codehaus.groovy.runtime.InvokerHelper Error** 오류가 발생하였다.
<br>
<br>

### **해결**
---
Gradle 5.x 버전에서 jdk14 버전을 지원히지 않는 과정에서 발생하는 오류였다.

Gradle 버전을 5.x 에서 6.x 버전으로 업그레이드하면 해결된다.

**gradle/wrapper/gradle-wrapper.properties**
```properties
# Gradle distribution version url

# old version
# distributionUrl=https\://services.gradle.org/distributions/gradle-5.6.4-bin.zip

# new version
#
distributionUrl=https\://services.gradle.org/distributions/gradle-6.3-bin.zip
```
