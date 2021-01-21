---
title: "Java RestTemplate File Upload"
date: 2020-08-10
categories: posts
layout: single
classes: wide
tags: ["java"]
---

### **How to**
---
RestTemplate 를 이용해서 파일을 전송하고자 할때 일반적인 방법으로 전송하게 되면 Jackson 라이브러리에서 직렬화에 실패했다고 에러 메세지를 보낸다. 그러면 어떻게 할 수 있을까? 파일 자체를 바이트로 변경해서 이것을 Multivaluemap 에 담아 보내는 방법이 있다. 코드는 다음과 같다.

```java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.MULTIPART_FORM_DATA);

MultiValueMap<String, Object> body = new LinkedMultiValueMap<String, Object>();

for (MultipartFile file : files)  {
  if (!file.isEmpty())  {
    body.add("file", new ByteArrayResource(file.getBytes()));
  }
}

HttpEntity<MultiValueMap<String, Object>> entity = new HttpEntity<MultiValueMap<String, Object>>(body, headers);

ResponseEntity<Map<String, Object>> result = restTemplate.exchange(
  "{api uri}",
  HttpMethod.POST,
  entity, 
  new ParameterizedTypeReference<Map<String, Object>>() {});
}
```

위 코드대로 보내면 Jackson FormHttpMessageConverter 관련 에러가 발생한다. 몇시간을 무슨 에러인지 헤매고 있었다. 자세히 에러 스택트레이스를 보니 무슨 컨버터가 자꾸 없다고 한다. 컨버터가 없다는게 무슨뜻이지.. 하고 생각하다가 아? RestTemplate 에 컨버터를 설정한 부분이 있었다는게 생각났다. 그래서 다시 구글링을 하니 다음과 같이 File 을 보내고자 할때에는 Resource 관련 컨버터를 추가하라는 말이 있었다.

```java
HttpMessageConverter<Object> jackson = new MappingJackson2HttpMessageConverter();
HttpMessageConverter<Resource> resource = new ResourceHttpMessageConverter();
FormHttpMessageConverter formHttpMessageConverter = new FormHttpMessageConverter();
formHttpMessageConverter.addPartConverter(jackson);
formHttpMessageConverter.addPartConverter(resource); 

RestTemplate restTemplate = new RestTemplate(Arrays.asList(jackson, resource, formHttpMessageConverter));
```

이 코드를 추가하니까 보내는건 잘보내진다. 하지만 받을때 에러가 발생! 뭐 이거는 어렵지 않게 해결할 수 있었다. 메세지를 보낼때 필요한 컨버터가 있다면 받을때도 내용을 가져올 컨버터가 설정이 되어 있거나 해야할것이다. 그래서 받는 API 에도 위 설정을 추가해주니 잘 되었다.

추가로 new ByteArrayResource 를 사용했는데, 스프링 5.1 버전 이후에서는 file.getResource() 를 사용하여 리소스를 보낼수 있다고 한다. 

**최종 수정 코드**

```java
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.MULTIPART_FORM_DATA);

MultiValueMap<String, Object> body = new LinkedMultiValueMap<String, Object>();

for (MultipartFile file : files)  {
  if (!file.isEmpty())  {
    body.add("file", file.getResource());
  }
}

HttpEntity<MultiValueMap<String, Object>> entity = new HttpEntity<MultiValueMap<String, Object>>(body, headers);

ResponseEntity<Map<String, Object>> result = restTemplate.exchange(
  "{api uri}",
  HttpMethod.POST,
  entity, 
  new ParameterizedTypeReference<Map<String, Object>>() {});
}
```

<br>

## **참고**
---

[How to send Multipart form data with restTemplate Spring-mvc](https://stackoverflow.com/questions/28408271/how-to-send-multipart-form-data-with-resttemplate-spring-mvc/59054323#59054323)


[Resttemplate form/multipart: image + JSON in POST](https://stackoverrun.com/ko/q/8020530)