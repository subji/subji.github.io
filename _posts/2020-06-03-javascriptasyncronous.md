---
title: "Javascript 비동기란?"
date: 2020-06-03
categories: posts
tags: ["javascript"]
---

### 비동기란?
---
영어로는 Asyncronous, 특정 함수 또는 코드의 실행 완료와 상관없이 다음 코드 및 함수를 실행하는 방식이다. 

반대말로는 Syncronous, 동기 방식이 있다. 동기방식은 가까운 Java 를 보면 알 수 있다.

다음 코드를 확인해보면 출력이 println() 함수의 호출 순서대로 나오는것을 볼 수 있다. 이처럼 순차적으로 코드가 실행되는 방식을 동기 방식이라고 한다.

```java

public static void main (String[] args) {
  System.out.println("1");
  System.out.println("2");
}

// 1
// 2

```

그러면 비동기 방식은 어떻게 동작할까?

다음 에제를 보자.

```ts
console.log('1');

setTimeout(function ()  {
  console.log('2');
}, 1000);

console.log('3');

// 1
// 3
// 2
```
위 코드는 자바스크립트에서 대표적인 비동기 처리 함수로 볼 수 있는 setTimeout 를 사용한 예제이다. 

그렇다면 위 예제에서의 출력 순서는 어떻게 될까? 일반적인 경우라면 당연히 위에서부터 아래로 읽어내려가며 1 -> 2 -> 3 으로 생각될 것이다.

하지만 비동기 방식에서는 그렇지 않다. 첫번째 1 이 출력되고 2가 출력되기전까지의 1초를 기다려주지 않고 다음 3을 먼저 출력한다.

이렇게 순서에 상관하지 않고 코드를 실행하는 방식을 비동기방식이라고 한다.
<br><br>

### 비동기 처리를 동기? 적으로 사용하고 싶을떄
---
앞서 말한 비동기 방식을 동기방식으로 사용하고 싶을때 사용할 수 있는 몇가지 방법이 있다.

첫째로 콜백을 사용하는 방식이다.

자바스크립트에서 함수는 일급객체로서 파라미터로 사용할 수 있다.

그래서 함수 호출이 파라미터로 콜백함수라는 또다른 함수를 포함시켜 동기식으로 사용하는 방법이있다.

JQuery 함수로 익숙한 Ajax 를 활용한 예제 코드를 보자.

참고로 Ajax 는 클라이언트에서 서버로 XMLHttpRequest 를 비동기로 처리하는 통신방식이다.

```ts
// 1. 
function main ()  {
  let result = null;

  $.get('/test', function (res) {
    result = res;
  });

  return result;
}

console.log(main());

```

```ts
// 2.
function main (callback)  {
  $.get('/test', function (res) {
    callback(res);
  });
}

main(function (res) {
  console.log(res);
});

```

1번과 2번 에제를 비교해보자. 1번은 비동기방식으로 처리되는 코드를 그대로 사용한 것이다. "console.log(main())" 의 결과는 어떻게 나올까? 응닶 값이 나올까? 아니면 null 값이 나올까? 답은 null 값이 나온다. 이유는 비동기 방식에서 찾아 볼 수 있다. $.get 으로 호출한 함수를 자바스크립트는 해당 요청이 완료될때까지 기다려주지 않고 바로 다음 코드인 "return result" 를 실행한다. 그래서 결과적으로 마지막 로그에서는 null 이 출력되는 것이다.

이러한 문제점을 해결하고자 하는 방식중에 하나가 콜백함수를 이용한 방식이다. 같은 Ajax 예제인 2번을 보면 이름이 없는 함수를 파라미터로 main 함수에 전달해주고 있다. 자바스크립트에서 함수는 일급객체이므로 파라미터로 사용이 가능하다. 

이 실행 순서를 1번 예제와 비교해보자.

1. main 함수 호출 -> $.get 요청 -> 결과(result) 반환 -> 결과(result) 출력 -> $.get 요청 응답? (순서가 마지막이 될지 언제가 될지 모른다.)
2. main 함수 호출 -> $.get 요청 -> 응답 -> 콜백함수로 반환

순서를 보면 2번의 경우 일반적으로 생각하기 쉬운 처리 방식인 위에서부터 아래로 읽어내려가는 방식이다. 반면에 1번의 경우는 도통 이해가 잘 가지 않는 방식이다.

콜백 방식은 비동기방식을 조금이나마 동기에 가깝게? 사용할 수 있도록 도와준다. 하지만 꼭 좋은점만 있는건 아니다.
<br>
<br>
### 콜백의 단점?
---
콜백함수의 경우 비동기방식을 조금이나마 해소할 수 있는 방법이다. 하지만 가장 큰 문제로 꼽히는것이 콜백지옥이라고 불리는 문제이다.

다음 예제로 확인해보자.

```ts
function main (callback)  {
  callback();
}

main(function ()  {
  main(function ()  {
    main(function ()  {
      ...
    });
    ...
  });
});
```
예제에서는 같은 함수를 계속 호출하였지만 각각 다른 함수를 호출할 수도 있다. 그럼 이게 왜 지옥이라고 불리느냐? 이유는 간단하다. 지금이야 몇개 안되는 함수를 콜백으로 호출하기 때문에 알아보기 쉽지만 만약 10개가 넘어가고 또 20개가 넘어가고 하다보면 알아보기 힘든 코드가 될 것이다.

더군다나 디버깅에도 어려움이 있다. 

이를 해소할 수 있는 방법이 몇가지 또 있는데 첫째로 앞서 비동기 처리방식을 동기방식으로 해결하고자 도움을 줄 몇가지 중에서 Promise 와 Async/Await 가 있다.
<br><br>
### Promise ?
--- 
조금 낯선? 용어다. Promise? 약속? 뭘 약속한다는거지? 

이전에 봤듯이 콜백함수를 사용한 비동기 처리에서는 콜백지옥에 빠져 코드를 디버깅하기도 읽기도 어려워진다는 단점이 있었다. 이를 해결하기 위해서 사용되는것이 Promise 라는 함수이다.

Promise 는 총 3가지의 상태가 있다.
- 대기 (Pending) 
- 이행 (Fullfill)
- 실패 (Reject)

1. 대기 상태
- new Promise() 로 선언된 상태이다.
```ts
new Promise();
new Promise(function (resolve, reject) {

});
```
2. 이행 상태
- 콜백함수를 실행시켜주는 단계이다.
```ts
new Promise(function (resolve, reject)  {
  // 이행 콜백 함수 호출
  resolve();
});

function main() {
  return new Promise(function (resolve, reject) {
    resolve('Success');
  });
}

main()
// 이행 콜백 함수 
.then(function (result) {
  console.log(result);
});
```
3. 실패 상태
- 이행 상태는 Resolve 호출, 실패는 Reject 함수를 호출한다.
```ts
new Promise(function (resolve, reject)  {
  // 이행 콜백 함수 호출
  resolve();
  // 실패 콜백 함수 호출
  reject();
});

function main() {
  return new Promise(function (resolve, reject) {
    resolve('Success');
    reject('Error');
  });
}

main()
// 이행 콜백 함수 
.then(function (result) {
  console.log(result);
})
// 실패 콜백 함수
.catch(function (error)  {
  console.error(error);
});
```

위 예제들 처럼 성공시 이행을 동작하고자 할때에는 resolve 함수를 호출하고 then 메소드에서 처리하며, 실패를 동작하고자 할때에는 reject 함수를 호출하고 catch 에서 해당 실패 이유를 확인한다.

이렇게 간단하게 예제를 알아보았다. 조금 더 알아보자면, 한개의 Promise 를 여러개의 then 을 사용하는 방법과 여러개의 Promise 를 연결하는 방법이 있는데 그 예는 다음과 같다.

```ts
function main ()  {
  return new Promise(function (resolve,reject) {
    resolve();
  });
}
// 한개의 Promise 에 여러개의 then 을 사용하는 방법
main().then(function () { return result; }).then(function (result) { return result; })

function subPromise ()  {
  return new Promise(function (resolve, reject) {
    resolve();
  });
}

main().then(subPromise).then()...

```

마지막으로 Promise 에서의 에러처리는 catch() 함수를 사용하도록 하자.
<br><br>
### Async/Await
---
콜백함수, Promise 와 같이 비동기 처리 방법이다.

사용법은 매우 간단하다. 한가지만 주의하면 되는데 await 로 받는 함수는 반드시 Promise 객체로 리턴을 해줘야한다는 것이다.

예제를 하나보자.

```ts
function returnResult ()  {
  return new Promise (function (resolve, reject)  {
    resolve('Success');
  });
}

async function main ()  {
  let result = await returnResult();

  console.log(result);
}
```