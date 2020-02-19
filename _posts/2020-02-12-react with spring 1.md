---
title: "Spring Boot (Maven) with React Study day 1"
date: 2020-02-12
categories: posts
tags: ["spring boot", "maven", "react", "Spring Boot & React 연동"]
---
<br>
**목적**
===
- Spring boot 와 React 를 활용하여 간단한 웹 페이지를 만들어보고자 한다.

## **1. React JS 란?**
---
- 페이스북에서 개발한 라이브러리
- UI 를 컴포넌트화하여 재사용에 용이함
- Virtual DOM 이라는 개념을 사용
- JSX 
> ### **1-1. Virtual DOM?**
- UI 의 가상의 DOM 객체를 메모리에 저장하고 이를 라이브러리를 통해 실제 DOM 에 동기화 하는 프로그래밍 개념
- 이와 같은 과정을 재조정이라고 한다.
- 기존의 DOM 의 경우 페이지를 로드할때 재구성되는 과정에서 성능이 낮은데, Virtual DOM 의 경우 동적인 페이지 로드 환경에서 이전값과 현재값을 비교하여 차이가 있는 부분만 변경해주기 때문에 성능상 이점이 있다.
- 단, DOM 의 경우 무조건 느린 것이 아닌 ReFlow 현상이 잦은 페이지에서 성능 저하가 나오며 그외의 ReDraw 상황 또는 데이터 변화가 적은 환경에서는 성능 차이가 없다.
- Virtual DOM 의 경우 데이터의 잦은 변화가 있는 페이지에서 유용하다는 점을 주목하여 이에 맞게 사용하는 편이 낫다.
> ### **1-2. JSX?**
- Javascript 확장 문법
- Javascript 코드 안에서 UI 관련작업을 할때 도움이 되며, React 의 에러 처리에도 도움
- React 의 이벤트 처리방식, 상태가 변하는 방식, 데이터를 화면에 보여줄 방식등 로직과 마크업을 컴포넌트에 묶어 사용
```javascript
  const name = 'Tester';
  const element = <h1>Hellow, {name}</h1> // JSX, name 변수를 JSX 안에 넣어주었다.

  ReactDOM.render(element, document.getElementById('root'));
```
- 모든 Javascript 표현식을 사용할 수 있다.
```javascript
function formatName (user)  {
  return user.firstName + ' ' + user.lastName;
}

const user = { firstName: 'First', lastName: 'last' };

const element = (
  <h1>
    Hello, {formatName(user)} <!-- Javascript 함수 사용 -->
  </h1>
)

ReactDOM.render(element, document.getElementById('root'));

```
- JSX 도 하나의 Javascript 객체이며, **if,for loop** 로 사용이 가능하다.
- MarkUp 과 같이 Attribute 를 태그안에 넣어줄수 있으며, 변수 역시 삽입이 가능하다. 단, 변수 삽입 시에는 **""** 를 제외하고 중괄호로 감싼다.
- Attribute 이름은 JSX 의 특성상 Javascript 에 가깝기 때문에 camelCase 를 사용.
```javascript
const element = <img src={user.avatarUrl}></img>;
```
- ReactDOM 은 렌더링 전 모든 값을 이스케이프(&lt, ...) 하므로, 애플리케이션에서 명시적으로 작성되지 않은 내용은 주입되지 않는다.
- 모든 항목은 렌더링전 문자열로 변환되며, 이러한 특성으로 XSS 공격을 방지 할 수 있다.

## **2. React JS 체험**
---
> ### **2-1. 세팅**
- NodeJS 설치 : [https://nodejs.org/ko/](https://nodejs.org/ko/) 에서 LTS 버전으로 설치하였다.
- NPM 설치 : NodeJS 를 설치하면 NPM 이 자동으로 설치된다.
- 설치 확인
```bash
  $ node -v 
  $   v10.16.0
  $ npm -v
  $   6.13.7
```
> ### **2-2. React 프로젝트 생성**
- NPX : NPM 패키지를 실행하는 명령어
- create-react-app 로 프로젝트 생성
```bash
  $ npx create-react-app my-app
```
- 다음과 같은 프로젝트 구조가 생성
<br><br>
<div style="width: 100%; text-align: center;">
  <img src="../assets/images/react structure.PNG" style="height: 500px;"/>
</div>

- "npm start" 명령어로 실행
```sh
  $ npm start
```
- http://localhost:3000 으로 접속하고 다음 화면이 나오면 정상적으로 동작된것이다.
<br><br>
<div style="width: 100%; text-align: center;">
  <img src="../assets/images/initial scrren.PNG" style="height: 500px;"/>
</div>

## **정리**
---
1. React JS 는 라이브러리이다.
2. JSX, Virtual DOM 등을 사용한다.
3. 설치방법

## **다음에 정리할 내용**
---
- Spring Boot 설치
- Spring Boot & React 연동 