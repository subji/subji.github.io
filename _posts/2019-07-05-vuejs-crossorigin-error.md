---
title: "Uncaught (in promise) TypeError Cannot read property 'protocol' of undefined 에러 처리"
date: 2019-07-05
categories: posts
tags: ["vue"]
---

### "Uncaught (in promise) TypeError Cannot read property 'protocol' of undefined"

Vue 2.x 버전에서는 axios 를 사용할 때, Vue.use(axios) 와 같이 사용하면 위와 같은 에러가 발생한다.

그러므로 다음과 같이 사용해야 한다.

```js
Vue.use(axios); // Incorrect

Vue.prototype.$http = axios; // Correct
```