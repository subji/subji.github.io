---
title: "nohup 명령어 .log 파일없이 실행"
date: 2020-04-12
layout: single
classes: wide
categories: posts
tags: ["linux"]
---

일반적으로 명령어 끝에 "&" 를 붙여도 백그라운드 실행이 된다.

그렇지만 ssh 등 세션을 끊더라도 계속 백그라운드에서 유지하려면 nohup 을 사용하게 된다.

nohup 명령어를 실행하면 out 파일이 생기는데 이 파일을 안생기게 하는 방법과 백그라운드 명령어 각각의 의미를 알아보자.

- 명령어 끝에 & : 명령어를 백그라운드 작업으로 실행
- 2>&1 : stderr (표준에러) 를 stdout (표준출력) 으로 이동합니다. (&1 부분을 특정 파일 경로로 바꾸어 로그를 출력할 수 있다.)
- /dev/null : stdout (표준출력) 을 /dev/null 로 이동, 지운다는 의미로 보통 알고있음
- nohup : stty 가 끊긴 후에도 프로세스를 종료시키지 않음

```sh
# nohup.out 파일 생성 없이 백그라운드 작업 실행
$ nohup example.sh 1> /dev/null 2>&1 &
```