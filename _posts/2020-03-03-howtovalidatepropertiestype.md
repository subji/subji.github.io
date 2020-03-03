---
title: "React props type Validation 방법 (How to validate props type React JS)"
date: 2020-03-03
categories: posts
tags: ["react"]
---

props 를 받으면서 타입 체크를 할 필요가 있을때가 있다.

그럴때는 간단하게 다음과 같이 사용할 수 있다.

1. **props-type** import

```ts
import PropTypes from 'prop-types';
```

2. 가능한 유효 타입 및 작성 방법

```ts
Component.propTypes = {
  anyProp: PropTypes.any,           // 타입 상관 X
  boolProp: PropTypes.bool,         // Boolean 타입
  numberProp: PropTypes.number,     // Number 타입
  stringProp: PropTypes.string,     // String 타입
  funcProp: PropTypes.func,         // Function 타입
  arrayProp: PropTypes.array,       // Array 타입
  objectProp: PropTypes.object,     // Object 타입
  symbolProp: PropTypes.symbol      // Symbol 타입
};

export default Component;
```