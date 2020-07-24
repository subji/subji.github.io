---
title: "React props Default value 적용 방법 (How to set default value of props React JS)"
date: 2020-03-03
categories: posts
tags: ["React"]
---

props 에 대하여 기본값을 지정해야 할 필요가 있을때가 있다.

그럴떈 다음처럼 사용할 수 있다.

1. 사용방법

```ts
Component.defaultProps = {
  propsOne: 'Test',
  ...
};

export default Component;
```