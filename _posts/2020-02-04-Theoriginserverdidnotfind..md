---
title: "Javascript SNS(Social Media) 공유 방법 (How to share social media in javascript.)"
date: 2020-01-17
categories: posts
tags: ["Git"]
---

## | 문제
Eclipse 에서 Spring 프로젝트를 Tomcat 위에서 실행하였을때 다음과 같은 에러 화면이 나왔다.

"404 Not Found"

"The origin server did not find a current representation for the target resource or is not willing to disclose that one exists."

## | 과정
별다른 에러 로그가 출력이 나오지 않아 구글 검색을 하였다.

문제는 간단했다. 컨트롤러가 어떤 경로를 못찾는것.

컨트롤러 주소 매핑이 잘못되었나했지만 아니었다.

찾아보니 Web Context Path 가 잘못되있었다.

## | 결론
```html
IDE 또는 Context Path 를 수정하자.
```