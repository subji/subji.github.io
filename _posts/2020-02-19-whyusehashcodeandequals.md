---
title: "Java equals & hashCode"
date: 2020-02-19
categories: posts
tags: ["java"]
---

## | 문제
스프링 시큐리티를 적용하다가 Entity 객체의 값을 비교하는 과정에서 equals 와 hashCode 메소드를 재정의 해야 한다는 것을 알았다.
그러면 두 메소드를 재정의하는 이유는 무엇일까..?

## | equals
equals 는 객체를 비교한다. 쉬운 예로 Java 에서 문자열 비교가 있다.

```java
String a = "test";

if (a.equals("test")) {
  System.out.println("Same!");
} else {
  System.out.println("Wrong!");
}
```
위 코드처럼 문자열에 대해서는 쉽게 비교가 가능하다. 하지만 객체를 예로들면 어떠할까?

```java
class A {
    private String a;
    
    public A (String a) {
        this.a = a;
    }
}

class Main {
  public static void main (String args[]) {
    A a = new A("a");
    A aa = new A("a");

    System.out.println(a.equals(aa)); // false
  }
}
```
위 코드에서보면 A 라는 객체에 초기값으로 "a" 라는 문자열을 생성하고 있다. 그러면 두 객체는 같은것일까?
<br>
결론은 아니다 객체는 생성됨에따라 서로 다른 주소값을 가지고 있다.
<br>
그러므로 같은 값으로 초기화되어도 같을 수가 없다.
<br>
<br>
그러면 같은 값을 가지는 두 객체를 같다고 비교할순 없을까?
<br>
그 방법은 객체내에 equals 메소드를 Override 하는것이다.

```java
class A {
  private String a;
  
  public A (String a) {
      this.a = a;
  }

  @Override
  public boolean equals(Object obj) {
    if (this == obj)  {
      return true;
    }
    
    if (obj == null)  {
      return false;
    }

    if (getClass() != obj.getClass()) {
      return false;
    }

    A other = (A) obj;

    if (a == null) {
      if (other.a != null)  {
        return false;
      }
    } else if (!a.equals(other.a))  {
      return false;
    }

    return true;
  }
}

class Main {
  public static void main (String args[]) {
    A a = new A("a");
    A aa = new A("a");

    System.out.println(a.equals(aa)); // true
  }
}
```
equals 메소드를 A 객체내에 Override 하여 구현하였다.   
코드를 보면 객체를 파라미터로 받고 그 객체가 지금의 객체 그 자신이면 true 를 반환한다.   
또한 파라미터 객체의 값이 null 이거나 getClass 의 함수값이 다르면 false 를 반환한다.   
위 과정을 통과하고 나면 중요한 부분인 비교할 객체와 기존 객체의 비교를 진행한다.   
코드에서는 a 라는 객체의 프로퍼티로 비교를 한다.   
a 프로퍼티는 null 이거나 비교객체(파라미터 객체) 와 값이 다르면 false 를 반환한다.   
위 조건들에 해당이 되지 않는다면 최종적으로 같은 객체라고 판단하여 true 를 반환한다.   

위 내용을 보면 처음에 생성되는 객체는 주소값이 다르기때문에 엄연히 다른 객체라고 말했는데, 정확히 보면 저것도 다른 객체가 아닌가요? 라고 할 수 있다.   
물론 맞는 말이야 엄연히 보면 다른 객체이다.   
하지만 객체 자체를 하나의 값으로 보고 비교하고 이것을 토대로 다른 추가, 삭제, 수정등의 기능을 수행하기 위해서는   
객체가 지닌 프로퍼티를 비교하여 같은 객체인지의 여부를 판단하는것도 하나의 방법인 것이다.   
이와같은 방법은 주로 JPA 사용시 Entity 객체를 비교하고자 할때 사용된다.

## | hashCode
hashCode 또한 위에서 설명한 equals 와 같은 맥락이다.   
객체의 내용으로 두 객체가 같은지를 판단하는 메소드이다.

추가적으로 말하자면 equals 객체를 재정의할때는 반드시 hashCode 메소드로 재정의해주어야 한다.

그 이유는 다음과 같은 hashCode 규약으로 확인할 수 있다.
> - 프로그램 실행 중 같은 객체의 hashCode 를 여러번 호출하는 경우, equals 가 사용하는 정보들이 변경되지 않는다면, 언제나 동일한 값이 반환되어야 한다. 하지만 프로그램이 종료되어 다시 실행 될 경우에는 그럴 필요가 없다.
> - equals 메소드가 같다고 판정되면 hashCode 메소드도 같아야 한다.
> - equals 메소드가 다르더라도 hashCode 값이 같을 수도 다를 수도 있지만 hashTable 의 성능 향상을 위해서는 다른 것이 낫다.

위 예제 코드에서 작성한 equals 메소드가 포함된 객체는 같은 정보를 가지고 있기때문에 같은 객체라고 판정되었다.   
하지만 아직까지 엄연히 다른 객체이다.   
이는 앞서 말한 hashCode 의 두번쨰 규약을 위반하는 내용이다.   

```java
class A {
  private String a;
  
  public A (String a) {
      this.a = a;
  }

  @Override
  public boolean equals(Object obj) {
    if (this == obj)  {
      return true;
    }
    
    if (obj == null)  {
      return false;
    }

    if (getClass() != obj.getClass()) {
      return false;
    }

    A other = (A) obj;

    if (a == null) {
      if (other.a != null)  {
        return false;
      }
    } else if (!a.equals(other.a))  {
      return false;
    }

    return true;
  }
}

class Main {
  public static void main (String args[]) {
    A a = new A("a");
    A aa = new A("a");

    System.out.println(a.hashCode());   // 918221580
    System.out.println(aa.hashCode());  // 2055281021
  }
}
```

그러므로 더 명확하게 같은 객체임을 판정하기 위해서는 hashCode 메소드를 재정의 해주어야 한다.   
만약 HashMap, HashSet, HashTable 등의 객체를 사용할 때 이 객체들에 위애서 equals 만 재정의한 객체를 추가한다면 서로 다른 hashCode 를 가지는 객체이므로 같은 객체가 중복되어 추가되게 된다.

그러므로 hashCode 값을 비교 해줄 수 있게 hashCode 메소드를 재정의 해주어야 한다.

```java
class A {
  private String a;
  
  public A (String a) {
      this.a = a;
  }

  @Override
  public boolean equals(Object obj) {
    if (this == obj)  {
      return true;
    }
    
    if (obj == null)  {
      return false;
    }

    if (getClass() != obj.getClass()) {
      return false;
    }

    A other = (A) obj;

    if (a == null) {
      if (other.a != null)  {
        return false;
      }
    } else if (!a.equals(other.a))  {
      return false;
    }

    return true;
  }
  
  @Override
  public int hashCode() {
    final int prime = 31;
    int result = 1;
    result = prime * result + ((a == null) ? 0 : a.hashCode());
    return result;
  }
}

class Main {
  public static void main (String args[]) {
    A a = new A("a");
    A aa = new A("a");

    System.out.println(a.hashCode());   // 128
    System.out.println(aa.hashCode());  // 128
  }
}
```

결론적으로 VO, DTO, Entity 등의 객체를 비교하고자 할때에는 위처럼 equals, hashCode 메소드를 재정의 해주어야한다.   

추가적으로 lombok 을 사용할 경우에는 @EqualsAndHashCode Annotation 을 사용하여 코드를 간단하게 할 수 있다.