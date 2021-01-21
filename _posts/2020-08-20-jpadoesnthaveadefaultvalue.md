---
title: "java.sql.SQLException: Field 'id' doesn't have a default value 문제 해결"
date: 2020-08-20
layout: single
classes: wide
categories: posts
tags: ["jpa", "hibernate"]
---

### **Problem**
---
분명 연관관계 매핑도 제대로하고 테이블에도 Not Null 로 설정을 해두었다. 그런데 자꾸 'doesn't have a default value' 문제가 발생한다. 

<br>

## **How to solve?**
---
아무리 찾아도 코드상에는 오류가 없었다. 그렇게 끙끙 앓다가 스택오버플로우에서 한줄기 빛을 찾게 되었다. 

[Hibernate: “Field 'id' doesn't have a default value”](https://stackoverflow.com/questions/804514/hibernate-field-id-doesnt-have-a-default-value)

여기에서 100 표를 넘게 받은 답변을 보면 알 수 있다. 

"경우에 따라서 모델이나 ORM은 데이터베이스의 변경사항이 정확하게 반영되지 않을 수 있다. (SchemeUpdate),   
 코드상의 오류나 정확히 파악할 수 있는 오류가 아니고 원인을 알 수 없는 오류의 경우에는 데이터베이스 또는 테이블을 다시 만들고 재실행을 하는것을 권한다. (SchemeExport)"

그렇다. 나의 경우에도 정확히 원인을 뭐라고 말할 수 없을뿐더라 찾을 수도 없었기에 위 답변이 맞는말이었다. 그래서 오류가 나는 컬럼이 포함된 테이블을 드랍하고 다시 추가하였다.

서버를 재실행하니 잘된다.. 

<br>

## **Why?**
---
그렇다면 뭐가 문제일까 도대체?

대부분의 글에선 Hibernate 에서 발생하는 오류라기보단 특징? 이라고 한다. hibernate 는 DB를 추상화하는 도구로써 테이블이나 컬럼에 대해서 아주 정확한 매핑을 하지 않는다고 한다. 

그래서 데이터 타입이 다른데도 매핑시키거나 한다고 한다. 그래서 중간에 테이블이나 컬럼의 설정을 변경하면 반영이 안되는 것일 수도 있다고 생각한다.

추가로 테이블 또는 컬럼의 변경사항을 좀 더 잘 적용되게 하려면 hbm2ddl.auto 옵션에서 update 를 사용하면 된다고한다. 하지만 update 옵션에도 hibernate 의 느슨한 매핑 특징은 완전히 처리가 안되는것같다.

hbm2ddl.auto 의 옵션은 다음과 같다.

- **create**: SessionFactory 시작시 테이블 삭제하고 다시 생성
- **create-drop**: create + SessionFactory 종료시 테이블 삭제
- **update**: SessionFactory 시작시 컬럼 추가/삭제 수행. 데이터 삭제 안함
- **validate**: SessionFactory 시작시 테이블 컬럼 확인하여 다르면 예외 발생

<br>

## **참고**
---

[하이버네이트의 hbm2ddl.auto에 update가 좋을까? validate가 좋을까?](http://egloos.zum.com/gyumee/v/2483659)