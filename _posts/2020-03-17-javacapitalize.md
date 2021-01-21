---
title: "Java Capitalize"
date: 2020-03-17
layout: single
classes: wide
categories: posts
tags: ["java"]
---

자바에서 "command" => "Command" 로 변경하는 방법

```java
public String capitalize (String text)  {
  if (text == null) {
    return text;
  }

  return text.substring(0, 1).toUpperCase() + text.substring(1);
}
```