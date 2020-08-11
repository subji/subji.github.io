---
title: "Git fatal: refusing to merge unrelated histories 오류"
date: 2020-02-12
categories: posts
tags: ["git"]
---

## | 문제
- 기존에 있던 로컬 디렉토리에 깃 리포지토리를 연결하고 Push 또는 Pull 을 할때 <br>
"fatal: refusing to merge unrelated histories" 오류가 발생하여 동작하지 않았다.

## | 과정
- 이 오류는 로컬 디렉토리와 깃 리포지토리의 기록을 비교했을때 소스코드의 차이가 심할 경우 병합 (Merge) 의 오류가 발생할 것을 대비하여 발생시키는 오류이다.

## | 결론
- 두가지 방법이 있다.
- 첫번째, 현재 디렉토리의 내용을 전부 삭제하고 깃 리포지토리를 Clone 하는 방법
- 두번째, **--allow-unrelated-histories** 를 git pull 뒤에 입력하여 받아 오는 방법이다.
- 두번째의 경우 <U>관련없는 두 가지 (로컬 디렉토리, 깃 리포지토리) 의 기록의 병합을 허용</U>하는 옵션이다.

```shell
  $ git pull origin {branch name} --allow-unrelated-hitories
```