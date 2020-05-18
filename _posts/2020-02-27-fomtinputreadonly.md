---
title: "JSP 렌더링 오류 (java.lang.NoSuchMethodError: org.springframework.web.servlet.tags.form.InputTag.setReadonly(Ljava/lang/String;)V)"
date: 2020-02-27
categories: posts
tags: ["JSP"]
---

## | 문제
프로젝트를 업데이트하고 배포하였는데, 특정 페이지만 안들어가진다..

더군다나 해당 페이지는 건드린적도 없었다.

레거시 프로젝트라 하나하나 다 살펴봐야 하는 상황..

다음과 같은 에러 발생

<div style="width: 100%">
  <img src="https://subji.github.io/assets/images/202002271223-fomtinputreadonly.PNG">
</div>

## | 체크리스트
- 컨트롤러에서 뷰로 매핑이 잘못되었나 ? - **NO**
- 뷰리졸버를 설정하는 과정에서 오류가 있나 ? - **NO**
- 뷰 파일의 위치가 잘못되었나 ? - **NO**

후.. 도대체 뭘까..   
하고 포기하면서 로그나 읽어보자 했는데..!?

로그 마지막 4문장 정도를 보면 configuration.jsp 라는 파일에서 input 태그의 readonly 속성이 잘못됬다고 나와있었다!

다시 구글링해서 다음과 같이 명쾌한 해답을 얻었다.

## | 결론

- <form:input > 태그의 readonly 속성이 잘못되었던 것
- readonly 는 readonly="true" 로 설정할 것
- 그외에 속성을 설정하면 에러 발생!

코드를 보니까 <form:input readonly="readonly" > 로 되어있었다..

후.. 레거시 코드 ... 

그래도 고쳐서 다행이다.