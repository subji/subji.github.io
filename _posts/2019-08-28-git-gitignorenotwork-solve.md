---
title: ".gitignore가 적용되지 않는다"
date: 2019-08-28
categories: posts
tags: ["git"]
---
git 푸시를 할때 가끔 gitignore 가 제대로 동작하지 않는 경우가 있다.

이 경우 git 캐시를 지우고 다시 푸시해주자
```
git rm -r --cached .
git add .
git commit -m '깃 이그노어가 적용되지 않아서 다시 올려요~'
```
출처: [My .gitignore file is ignored by git and it does not work](https://bytefreaks.net/programming-2/my-gitignore-file-is-ignored-by-git-and-it-does-not-work)