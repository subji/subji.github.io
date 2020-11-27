---
title: "알아두면 좋은 정규식(Regular Expression) 모음"
date: 2020-11-27
categories: posts
tags: ["regular expression"]
---

### **업데이트 일자 2020-11-27**
---
- 학교 메일 검사 (ex. "ac.kr", "x.{학교 도메인}.ac.kr", 등등)
```regex
/[\@]{1}[a-z0-9A-Z\.]*(?=\.ac\.kr)/gi
```