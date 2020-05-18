---
title: "React WebFonts 적용하기 (Applied WebFonts on React)"
date: 2020-03-02
categories: posts
tags: ["React"]
---

## | 문제
기존 프로젝트에서 Web Fonts 를 사용하고 있었다.

리액트로 전환하는 도중 초기에 셋업과정에서 웹폰트 적용에 있어 오류가 발생하였다.

기존 프로젝트에서는 웹폰트를 불러오는데 별다른 제약이 없었지만 리액트에서는 따로 처리를 해줘야하는것 같았다.

그래서 구글링을 통해 찾아보았는데 몇가지 방법이 있었다.

## | 어떻게?
**첫번쨰** 는 index.html 에 css 파일을 링크해주고 뒤에 웹폰트를 명시해주는 방법이다.

```html
<link rel="stylesheet" href="../fonts/fonts.css?family=Titillium+Web:300,400,700" media="all">
```

코드에서 보듯이 index.html 에 임포트하는 css 파일 뒤에 폰트패밀리로 웹폰트를 파라미터로 해주었다. 뒤에 숫자는 폰트의 사이즈를 의미한다.

위에 방법은 확실히 간단한 방법이다. 하지만 다음과 같은 이슈가 있다고 한다.

해당 라인에 대해서 퍼포먼스에 관련된 이슈이다.

[Render Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css)

그래서 **두번째** 로 다음과 같은 방법을 사용한다.

이 방법은 자체 로컬에 설치된 자체 웹폰트나 외부 웹폰트를 모두 사용가능하다.

[Web Font Loader](https://github.com/typekit/webfontloader)

먼저 라이브러리를 설치해주자. 설치할때는 항상 package.json 에 저장되게 --save 옵션을 넣어주자.

```sh
npm install --save webfontloader
```

다음과같이 index.js 등 원하는 스크립트 파일에서 작성한다.

```ts
import WebFont from 'webfontloader';

WebFont.load({
  google: {
    families: ['Titillium Web:300,400,700', 'sans-serif']
  }
});
```

웹폰트로더를 임포트하여 웹폰트를 불러오는 로직이다.

예시는 구글의 웹폰트를 불러오며 라이브러리의 깃허브 페이지를 보면 알 수 있듯이 여러가지 웹폰트도 불러올수 있다.


```js
WebFont.load({
  google: {
    families: ['Titillium Web:300,400,700', 'sans-serif']
  },
  custom: {
    families: ['curstom-bold'],
    urls: ['../fonts/fonts.css']
  }
});
```

사용자 정의 웹폰트는 예시처럼 설정할 수 있다.

**Before**
<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202003021659-howtoappliedfontsonreact1.png">
</div>

**After**
<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202003021700-howtoappliedfontsonreact.png">
</div>