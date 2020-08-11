---
title: "D3 여러 버전 사용하는 방법 (How to use multiple version d3.js)"
date: 2020-03-23
categories: posts
tags: ["javascript", "d3js"]
---

D3 를 사용하는데 레거시 프로젝트에서 v3 과 v4 를 같이 사용하고 있었다.

기존 레거시 프로젝트는 다른 jsp 에서 각각 다른 버전을 사용하고 있어서 오류가 없었다.

하지만 같은 페이지에서 v3 과 v4 를 써야하는 상황이오니 v3 -> v4 에서 버전업 되면서 발생하는 호환성 이슈가 폭발했다.

그래서 2개를 각각 사용하는 방법을 생각해봤다.

전역으로 불러오거나 지역으로 불러오거나 상관없이 다음과 같이 해주자.

```html
<script type="text/javascript" src="d3.v3.js"></script>
<script>
  d3v3 = d3;
  window.d3 = null;
</script>
<script type="text/javascript" src="d3.v4.js"></script>
<script>
  d3v4 = d3;
  window.d3 = null;
</script>
```