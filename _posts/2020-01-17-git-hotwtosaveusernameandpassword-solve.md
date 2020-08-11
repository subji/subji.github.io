---
title: "Git username, password 저장 방법 (how to save username and password in git)"
date: 2020-01-17
categories: posts
tags: ["git"]
---

## | 문제
git pull, git push 하는데 있어서 계속 비밀번호를 입력해야되서 번거로웠다. 그래서 비밀번호를 입력하지 않고 저장해서 사용하는 방법을 찾아보았다.

## | 과정
- git config credential.password={password} => 아예 명령어가 안된다.

## | 결론
다음 명령어를 먼저 실행한다. 전체에 공통 유저이름과 비밀번호를 저장하고 싶으면 --global 메뉴를 붙여준다.
```bash
  git config (--global) credential.helper store
```
그리고 나서 다음 명령어를 실행한다.
```bash
  git pull
```
이제 마지막으로 username 과 password 를 입력하면 저장이 된다.