---
title: "Oracle OVER 명령어"
date: 2020-05-18
categories: posts
tags: ["Oracle"]
---
쿼리 작업을 하다보면 SELECT 절에서 COUNT() 값을 같이 조회해서 내보내고자 할때가 있다.

이전에는 다음과 같이 서브쿼리를 사용해서 작성했었다.

```SQL
  SELECT TITLE,
         (
           SELECT COUNT(*)
             FROM T_TEST_ONE
         )
    FROM T_TEST_TWO
```

그런데 오라클에서는 좀 더 간단하게 표현하는 방법이 있었다.

바로 <b>OVER()</b> 함수다.

OVER 함수는

- GROUP BY 또는 ORDER BY 와 같이 그룹 조회를 할때 이용되는 서브쿼리를 개선하기 위해 나온 함수이다.
- COUNT(), SUM(), AVG(), RANK(), ROW_NUMBER() 등의 집계함수 또는 분석함수와 같이 사용된다.

아래는 일반적으로 발생할 수 있는 오류 상황이다.

```SQL
  SELECT COL_ONE,
         COUNT(*)
    FROM T_TEST_ONE
```

무엇이 잘못되었을까?

COUNT() 함수는 집계함수다. 즉, 전체를 그룹으로 하거나 특정 Column 을 그룹으로 만들어 사용할 수 있다.

위에 코드에서는 COL_ONE 이라는 Column 과 테이블 전체를 그룹으로 하는 COUNT 함수를 조회하고 있다.

즉, 집계함수를 사용하는 SELECT 문에서는 GROUP BY 로 지정되어 있지 않은 Column 을 조회할 수 없다.

만일 COL_ONE 이라는 Column 을 조회하고 싶으면 다음과 같이 작성해야 한다.

```SQL
  SELECT COL_ONE,
         COUNT(*)
    FROM T_TEST_ONE
   GROUP BY COL_ONE
```

자, 확인해보자!

실행결과는 오류없이 나오는것 같다. 하지만 문제는 GROUP BY 로 묶여진 COL_ONE 별 COUNT() 함수의 값이 집계 된다는 것이다.

내가 원하는 것은 COUNT() 함수는 전체의 값을 COL_ONE 은 각각의 값을 내보내는 것이었다.

그래서 사용할 수 있는 것이 서브쿼리! 또는 OVER() 함수이다

OVER() 함수의 사용법은 간단하다.

```SQL
  # COL_ONE 과 테이블 전체의 COUNT() 값 조회
  SELECT COL_ONE
         COUNT(*) OVER()
    FROM T_TEST_ONE

  # 중복 제외
  SELECT COL_ONE
         COUNT(*) OVER(DISTINCT COL_TWO)
    FROM T_TEST_ONE

  # COL_TWO 정렬 
  SELECT COL_ONE
         COUNT(*) OVER(ORDER BY COL_TWO)
    FROM T_TEST_ONE

  # GROUP BY
  SELECT COL_ONE
         COUNT(*) OVER(PARTITION BY COL_THREE)
    FROM T_TEST_ONE

```

서브쿼리 사용도 좋지만 OVER() 함수와 같이 사용하기 편한 표현도 사용해보자.