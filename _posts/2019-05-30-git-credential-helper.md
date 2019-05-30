---
title: "Git 비밀번호 입력 없애는 방법"
date: 2019-05-30
categories: posts
tags: [git] 
---

1. Git Credential 정보 저장 
```
git config --global credential.helper store
```

2. Git Credential 정보 캐시에 저장 (시간 설정 가능)
```
git config --global credential.helper cache & 'cache --timeout 86400(1일)'
```

# Window 의 경우에는 아래 주소의 프로그램을 받아 설치 후 적용해본다.
https://github.com/microsoft/Git-Credential-Manager-for-Windows
