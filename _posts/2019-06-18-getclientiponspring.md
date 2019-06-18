---
title: "SPRING 에서 PROXY 된 IP 까지 받아오는 방법"
date: 2019-06-18
categories: posts
tags: [SPRING] 
---

기본적으로 HttpServletRequest.getRemoteAddr() 로 받아 올 수 있지만

Proxy 된 IP 의 경우 제대로 받아오지 못한다.

이때 아래의 코드를 사용하여 받아오도록 하자.

단, VPN 을 통한 IP 는 받아오지 않는다.
 
```
	HttpServletRequest.getHeader("X-Forwarded-For");
	HttpServletRequest.getHeader("Proxy-Client-IP");
	HttpServletRequest.getHeader("WL-Proxy-Client-IP");
	HttpServletRequest.getHeader("HTTP_CLIENT_IP");
	HttpServletRequest.getHeader("HTTP_X_FORWARDED_FOR");
```
