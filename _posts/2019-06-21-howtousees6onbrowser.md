---
title: "브라우저에서 ES6 사용 방법"
date: 2019-06-21
categories: posts
tags: ["Javascript"] 
---

vuejs 를 사용하다가 import & export 문을 사용할 경우가 생겨 사용하던 도중

정의되지 않은 식별자라고 오류가 나와서 찾아보았다.

검색 결과 이전에 es6 구문을 사용하기 위해서 필요했었던 babel, webpack 없이도

사용할 수 있었다.

방법은 간단하다. 아래 코드에서 처럼 스크립트 태그에 "module" 만 추가해주면 된다.

~~~html
<script type="module" src="...">
~~~

지원하는 브라우저
- **[IE]** X
- **[Edge]** 17+
- **[Edge Chromium]** 75+
- **[Chrome]** 72+
- **[Firefox]** 66+
- **[Safari]** 12.1+
