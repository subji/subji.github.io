---
title: "Vue.js external script(외부 스크립트) 추가 방법"
date: 2019-07-05
categories: posts
tags: ["vue"]
---

스프링 & 뷰 프로젝트를 하던 도중 굳이 외부 스크립트를 추가해야 할 일이 생겼다.

이런저런 방법을 동원해도 스크립트가 추가가 안되었는데 다음과 같이 했더니

잘 동작 되었다.

```js
// vue.config.js
module.export = {
  publicPath: process.env.NODE_ENV === 'production' ? './' : '', // 정적 리소스의 경로를 상대 경로로 지정한다.
  outputDir: '../backend/src/main/resources/static', // 정적 파일 생성 위치
  assetsDir: '', // js, css, img 파일 생성 위치
  indexPath: '../templates/index.html', // index.html 파일이 생성되는 위치
  devServer: {
    proxy: {
      '/': {
        target: 'localhost:8090',
        ws: true,
        changeOrigin: true
      }
    }
  }
}
```

vue 설정은 위와 같이 하고

vue-cli 디렉토리 중에 public 디렉토리에 원하는 리소스 파일들을 넣는다.

그리고 아래와 같이 vue 파일에 작성한다.

```js
// xxx.vue
export default {
  methods: {
    addScript: function () {
      let scripts = [
        '스크립트 상대 경로',
        'ex) libs/jquery/jquery.js',
      ];

      for (let i = 0, len = scripts.length; i < len; i++) {
        let script = document.createElement('script');
        script.setAttribute('type', 'text/javascript');
        script.setAttribute('src', scripts[i]);
        script.async = true;
        document
        .getElementsByTagName('body or head')[0]
        .appendChild(script);
      }
    }
  },
  created () {
    this.addScript();
  }
}
```
마지막으로 빌드하면 스프링 부트 resources 디렉토리에 static & templates 디텍토리안에 vue 에서 빌드한 파일이 생긴다.

```shell
npm run build
```

이제 스프링을 실행하면 성공이다.