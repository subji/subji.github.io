---
title: "Git error: src refspec master does not match any"
date: 2019-08-28
categories: posts
tags: ["git"]
---
Git 푸시 도중 아래와 같은 에러가 발생하였다.
```
error: src refspec master does not match any
error: failed to push some ref to 'URL'
```
위 에러는 푸시를 할때, 커밋된 내용이 없기때문에 깃 서버에 추가할 
브랜치가 존재하지 않아 발생하는 것이다.

즉, 빈 리포지토리 (예. 초기에 리포지토리를 생성한 경우)일 때, 아무런 커밋 없이
푸시를 하였을때 발생한다.

이는 커밋을 하고 푸시를 하면 쉽게 해결이 가능하다.