---
title: "SyntaxError: Cannot use import statement outside a module"
date: 2020-06-03
layout: single
classes: wide
categories: posts
tags: ["javascript", "typescript", "node"]
---

NodeJs 와 Typescript 를 사용해서 개발을 하던 도중 제목과 같은 오류를 마주하게 되었다.

module 밖에서 추가된 코드를 사용할 수 없다는 말인거 같다.

그래서 첫째로 tsconfig.json 을 살펴보았다. module: commonjs 를 보면 commonjs 를 활용해서 module 을 사용하고 있었다. commonjs 라 함은 기억이 가물가물하지만 require('') 로 선언해서 외부 라이브러리를 가져오는 방법인걸로 알고있다. 

그러면 뭐가 문제일까..?

nodejs 공식문서를 보면 다음과 같은 문장이 있다. 

`If the nearest parent package.json lacks a "type" field, or contains "type": "commonjs", .js files are treated as CommonJS. If the volume root is reached and no package.json is found, Node.js defers to the default, a package.json with no "type" field.`

package.json 에서 "type" 필드에 별도의 값이 없거나 "commonjs" 로 설정되어있으면 기본 모듈로 "commonjs" 를 사용한다고 나와있다. 그럼 어떻게 import 문을 사용할 수 있을까? 그 답은 쉽게 찾을 수 있었다. package.json 에서 "type": "module" 로 설정하면 되는것이었다.

문제해결!