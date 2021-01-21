---
title: "CRLF (Carriage Return & Line Feed) 란?"
date: 2020-05-18
layout: single
classes: wide
categories: posts
tags: ["computer theory"]
---
Git add 를 하다가 다음 경고가 출력되었다.

"he file will have its original line endings in your working directory
warning: CRLF will be replaced by LF in"

CRLF 는 개행문자인데 무언가 잘못된건가 싶었다.

찾아보니 큰 문제는 아니었고 OS 별로 CRLF 를 판정하는 기준이 조금씩 달라서 생긴 경고였다.

이 경고는 간단하게 다음 명령어를 입력하면 수정가능하다.

```sh
git config --global core.autocrlf false
```

그럼 본론으로 들어가서 위에서 언급한 CRLF 는 무엇일까?

간단하게 개행문자라고 할 수 있다.

보통 글을 쓰다보면 다음 행으로 내려서 쓰는 경우가 있다. 이때 다음행으로 넘어가는 행위를 개행이라고 하는데,

컴퓨터는 사람처럼 자동으로 알아서 다음 줄을 만들어내질 못한다.

그래서 입력해주는 사람이 컴퓨터에게 알려주어야 한다. 이것이 바로 개행문자인데 ASCII 코드에도 10, 13 번으로 명시되어 있다.

CRLF 는 Carriage Return 과 Line Feed 로 이루어진 줄임말인데, Line Feed 는 Enter 키 입력


즉, 다음줄로 넘어가 달라는 명령문자이다.

Carriage Return 은 커서의 위치를 현재 줄의 맨 처음으로 이동시키는 명령문이다.

이 두가지 명령문자들은 타자기에서 유래했다고 한다.

영화속 타자기를 보면 주인공이 타자를 치다가 왼쪽 또는 오른쪽의 레버(?) 같은 모양을 내리거나 올리거나 한다.

이 동작이 바로 타자기에서 사용되는 Line Feed 알림인 것이다!

또한 같은 장면에서 종이가 왼쪽으로 점점 이동하면서 글을 작성하는데, 위에서 처럼 레버를 내리고 새로운 줄의 처음으로 

이동하기 위해 또다른 어떤 동작을 한다. 이것이 바로 Carriage Return 이다.