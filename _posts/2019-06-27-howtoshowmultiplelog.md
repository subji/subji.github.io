---
title: "tail 명령어로 여러개의 로그파일 보기"
date: 2019-06-27
layout: single
classes: wide
categories: posts
tags: ["linux"]
---

### 아래 명령어를 입력하면 여러개의 로그파일을 로깅할 수 있다.
### 로그 내용의 변화가 있을 경우 파일명과 로깅내용이 나오게 된다.
### (로그파일에 변화가 없는 경우 출력 X)
```bash
tail -f *.log
```