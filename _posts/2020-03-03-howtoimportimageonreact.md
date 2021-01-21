---
title: "React Image Import 하는 방법 (How to import image on React JS)"
date: 2020-03-03
layout: single
classes: wide
categories: posts
tags: ["react"]
---

리액트로 프로젝트를 만들면서 이미지를 어떻게 불러와야 하느냐에 대한 고민이 있었다.

처음에는 js, css 처럼 임포트문을 사용해서 넣어도 봤지만 이렇게하다가는 이미지가 한두개도 아니라서 코드가 복잡하고

불편해질거 같았다.

그래서 찾아보니 public 디렉토리를 자동으로 리액트 앱에서 검색하는거 같았다.

결과적으로 public 디렉토리 아래에 images 디렉토리를 만들고 해당위치를 상대경로로하여 불러올 수 있었다.

```html
<img src="/images/logo.png" />
```

위의 코드는 public 디렉토리 아래에 images 디렉토리가 있을 경우에 작동한다.