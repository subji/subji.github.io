---
title: "역인덱스"
date: 2020-07-03
layout: single
classes: wide
categories: posts
tags: ["db", "sql"]
---

### **역인덱스**
---
하나의 row 에 긴 텍스트가 있고 이런 row 들이 수만, 수십만개로 이루어진 테이블에서 "LIKE %...%" 같은 구문을 이용해서 검색을 한다고 해보자.

결과는 어떻게 될까? LIKE 절은 인덱스를 타지 않는다고 한다. 그러면 검색 속도를 올리고 싶을 떄는 어떻게 해야할까?

많은 방법이 있겠지만 여기서 소개할 방법은 역인덱스라고 불리는 방법이다. 역인덱스라는 말이 참 처음 듣기에 어려워 보이는데 개념은 간단하다. 예를들어 "안녕 나는 누구누구야" 라는 문장이 있다고 하자. 보통의 테이블은 "안녕 나는 누구누구야" 라는 내용이 담긴 컬럼 자체에 인덱스를 설정한다. 하지만 역인덱스는 해당 문장을 분해해서 각각 분해된 단어 또는 문자들에 인덱스를 설정하는 것이다.

위에서 예를든 문장 "안녕 나는 누구누구야" 의 경우 2개 단위로 문자를 자른다고 하면 역인덱스 테이블은 다음과 같다.

```sql
CREATE TABLE T_INVERSE_INDEX (
  original_seq NOT NULL, # 원래 문장의 시퀀스 및 인덱스 정보
  separated_word NOT NULL # 분리된 문자
)
```

역인덱스 테이블에서 중요한건 *separated_word* column 이다. 분리된 문자에 인덱스를 거는 것이다.! 이렇게 하면 "LIKE '%누구%'" 라고 검색을 할때 원본 테이블이 아닌 역인덱스 테이블에서 인덱싱된 *separated_word* column 을 조회해서 속도를 올릴수 있다.

<br/>

### **장/단점**
---
장점은 뚜렷하게 증가하는 속도일 것이다. 하지만 단점도 있다. 역인덱싱을 위한 테이블을 따로 추가해줘야 하는 것과 텍스트가 1000개의 단어로 이루어져 있고 이를 2개씩 자른다고하면 엄청난 수의 row 가 생길것이며, 그로인해 역인덱스 테이블의 용량은 엄청나게 커질 것이다. 이외에도 여러 장/단점이 있을 수 있다. 

<br/>

### **FULLTEXT INDEX**
--- 
검색속도를 올리고자 하려면 FULLTEXT INDEX 를 사용하는 방법도 있다고 한다. 