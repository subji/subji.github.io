---
title: "Docker Got permission denied while trying to connect to the Docker daemon socket at... 에러 해결"
date: 2019-12-22
layout: single
classes: wide
categories: posts
tags: ["Docker"]
---
도커를 처음설치하고 실행 후 "docker ps -a" 를 입력해보니

****"Got permission denied while trying to connect to the Docker daemon socket at" 에러를 출력했다

권한이 없다는 문제인데 간단하게 현재 로그인한 유저에게 도커 권한을 주면 되었다.
  
**소스 코드 :**
```bash
sudo usermode -a -G docker $USER
sudo reboot
```