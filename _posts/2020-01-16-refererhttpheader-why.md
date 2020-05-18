---
title: "Referer 를 받아올 수 없는 경우들 (In cases where "Referer" does not work.)"
date: 2020-01-16
categories: posts
tags: ["HTTP"]
---

## | 문제
웹 어플리케이션을 만들면서 현재페이지에서 다음페이지로 이동후에 이전페이지로 가기위한 Referer 헤더를 받아오는데, 주소를 직접 입력해서 접근하면 이전페이지 정보인 Referer 를 Null 로 받아왔다.

## | 과정
- #### "Referer" 란?
  - Referer 요청 헤더는 현재 요청된 페이지의 링크 이전의 웹 페이지 주소를 포함하는 헤더이다.
- Interceptor 에서 가로채므로 Interceptor 에서 Referer 를 받아와보았다. => 실패
- 혹여나 세션은 받아올 수 있지 않을까하고 세션에 추가해서 받아보았다. => 실패

## | 결론
- 구글링한 결과 Referer 는 다음과 같은 경우에는 받아올 수 없다고한다.
1. 주소를 직접 입력한 경우
2. 즐겨찾기로 이동한 경우
3. location.href 로 이동한 경우
4. 참조되는 리소스가 로컬 "파일" 혹은 "데이터"의 URI인 경우
5. 안전하지 않은 HTTP 요청이 사용되고 참조 페이지가 보안 프로토콜(HTTPS)로 수신된 경우