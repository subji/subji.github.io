---
title: "unclosed regular expression 에러 처리 방법"
date: 2020-03-09
categories: posts
tags: ["React"]
---

리액트앱을 만들다가 앱에 영향을 미치는 오류는 아니지만

jshint 가 읽는과정에서 에러로 처리하는 경우가 있다.

보통 jsx 를 사용할때 생기는 거 같은데 에디터나 IDE 에서 jshint 의 전역 설정을 할 수 있지만

간단하게 주석처리만으로도 해결이 가능하다.

jshint 의 예외를 추가해주는 방법인데 원하는 코드영역 위, 아래에 아래와 같은 주석을 추가해준다.


```ts
  return (
    /* jshint ignore:start */
    <div>
    </div>
    /* jshint ignore:end */
  );
```