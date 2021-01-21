---
title: "MyBatis DBMS 별 LIKE 절"
date: 2020-05-19
layout: single
classes: wide
categories: posts
tags: ["sql", "mybatis"]
---
DBMS 별로 MyBatis 에서 LIKE 절을 사용하는 방법이 조금 다르다.

각 사용방법은 아래와 같다.

<b>MySql</b>

```sql
  SELECT *
    FROM TABLE
   WHERE COLUMN LIKE CONCAT('%', #{ Keyword }, '%')
```

<b>Oracle</b>

```sql
  SELECT *
    FROM TABLE
   WHERE COLUMN LIKE '%' || #{ Keyword } || '%'
```

<b>MsSql</b>

```sql
  SELECT *
    FROM TABLE
   WHERE COLUMN LIKE '%' + #{ Keyword } + '%'
```
