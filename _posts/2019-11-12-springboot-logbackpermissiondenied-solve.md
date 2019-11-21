---
title: "/tmp/spring.log (Permission denied)"
date: 2019-11-12
categories: posts
tags: ["spring", "springboot", "logging"]
---
하나의 프로젝트에서 여러개의 프로파일을 관리할때 /tmp/spring.log 가 같이 사용되어 권한 오류가 발생하는 경우가 있다.

보통 logback 의 기본설정 파일을 가져오면서 같은 위치를 가리키게 되어 생기는 오류인데,

이럴 경우 아래와 같이 해결할 수 있다.
```xml
<property name="LOG_TEMP" value="Log/" />
<include resource="org/springframework/boot/logging/logback/base.xml"/>
```
위 문구를 개별 logback 파일에 추가해주며, 주의사항을 반드시 property 태그가 base.xml 를 포함하는 태그보다

위에 와야 한다.
