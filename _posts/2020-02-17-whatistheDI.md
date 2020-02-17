---
title: "Spring Boot 필드 인젝션이 아닌 생성자 인젝션을 해야 하는 이유"
date: 2020-02-17
categories: posts
tags: ["spring boot"]
---

Field Inject 대신 생성사 주입을 해야하는 이유?

# 의존성 주입 (Dependency Indject) ?
프로그래밍에서 구성요소간의 의존 관계가 소스코드 내부가 아닌 외부의 설정파일등을 통해 정의되게 하는 디자인 패턴중의 하나.

## 의존성 ?
쉽게 말해 의지한다는 표현으로 받아 들일 수 있다.
<br>
B 클래스가 A 클래스를 의존하고 있고, A 클래스가 변하게 된다면 B 클래스 역시 변하게 되는 것을 말한다.
<br>
따라서 클래스간 영향을 많이 받을 경우 의존성이 높다고하며, 영향을 적게 받을 경우 의존성이 낮다고 한다.
## 주입 ?
내부가 아닌 외부 설정파일등을 통해 객체를 생성하고 무언가를 넣어주는 것을 말한다.

## 의존성이 높은 경우
```java
public Class A {

  public String str = "DI test";

  public void printA () {
    System.out.println(str);
  }
}

public Class B {
  public void printB ()  {
    A a = new A();
    a.printA();
  }
}

B b = new B();
b.printB();
```
위의 코드에서 보듯이 클래스 B 는 클래스 A 에 의존성이 강한 형태이다.   
무슨말인가하면 클래스 B 의 printB 함수는 클래스 A 의 printA 함수를 호출해준다.   
따라서 printA 의 함수의 변화에 따라 printB 함수의 값이 달라지게 된다.   
그러므로 클래스 B 가 클래스 A 에 의존해있다고 볼 수 있다.   
<br>
정리하면 위처럼 클래스 B 내부에서 클래스 A 를 직접 생성하고 있고 클래스 A 가 아닌 다른    
객체로 변경하고 할 때에 클래스 B 를 수정해야 하는 형태를 클래스 B 가 클래스 A 에 의존한하고 하며   
**강한 결합** 이라고 한다.

## 의존성이 낮은 경우
위와 같은 관계에서는 상위 객체인 B 가 하위 객체인 A 에 의존하고 있다.   
하지만 이럴 경우 의존도가 높아져 재사용성, 단위테스트 등의 의존성 주입의 장점이 무색하게 된다.   
그래서 의존도를 낮춰야 하는데 그 방법으로는 의존 관계 역전 원칙이 있다.   
<br>
**의존관계 역전 원칙** 이란 상위 객체가 하위 객체에 의존하는 관계를 분리하여 각각을 모듈 형태로 나누는 것이다.   
즉, 전통적인 상하위 관계의 의존성을 반전(역전) 시킴으로써 상위 객체가 하위 객체의 구현으로부터 독립되게 하는 것이다.   
<br>
이 원칙은 다음과 같은 내용을 가지고 있다.      
**첫째.** 상위 객체는 하위 객체에 의존해서는 안되고 모두 추상화에 의존해야 한다.    
**둘쨰.** 추상화는 세부 사항에 의존해서는 안되고 세부사항이 추상화에 의존해야 한다.   
-> 인터페이스를 활용한 의존성 주입 방법
<br>
### **인터페이스 (Setter) 주입 ?**
의존성 주입은 객체 생성을 외부로부터 주입받는 것이다. 
<br>
그 중 의존성을 낮출 수 있는 방법중에 하나인 인터페이스 주입은 다음과 같이 사용할 수 있다.

```java
public interface TestInterface {
  void print (String text);
}

public class A implements TestInterface {

  @Override
  public void print (String text) {
    System.out.println("A: " + text);
  }

}

public class B implements TestInterface {

  @Override 
  public void print (String text) {
    System.out.pritln("B: " + text);
  }

}

public class MainClass ()  {

  private TestInterface testInterface;

  public void setTestInterface (TestInterface testInterface)  {
    this.testInterface = testInterface;
  }

  public void printText () {
    TestInterface.print("Test");
  }

}

MainClass m = new MainClass();
TestInterface interA = new A();
m.setTestInterface(interA);
m.printText();
```
이처럼 구현했지만 뭔가가 부족하다. 아직 MainClass 객체와 A 객체는 소스코드 내부에서 생성을 해주고 있다.
<br>
이를 또 다시 밖으로 분리하려면 xml 파일을 사용하여 bean 으로 객체를 생성해주는 방법 또는 Spring Annotation 을 사용하는 방법이 있다.
<br><br>
Bean 을 사용하여 주입하는 방법은 다음과 같다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <!-- A a = new A(); -->
  <bean id="A" class="A.class" />
  <!-- B b = new B(); -->
  <bean id="B" class="B.class" />
  <!-- MainClass m = new MainClass(); -->
  <bean id="Main" class="Main.class">
    <!-- m.setTestInterface(interA) -->
    <property name="interA" ref="A" />
  </bean>

</beans>
```
이제 호출하는 쪽에서는 다음과 같이 사용한다.

```java
public static void main (String args) {
  ApplicationContext ctx = new ClassPathXmlApplicationContext("test.xml");
  MainClass m = (MainClass) ctx.getBean("Main");
  m.printText();
}
```
Interface 주입 방법 (Setter 를 이용한 주입 방법) 은 이처럼 객체를 분리하여 하위 객체의 메소드를 가져오는 상위 객체의 의존성을 낮춰준다. 
<br>
하지만 문제점은 하위 객체를 생성하여 setter 함수로 넘겨주는 것만으로도 상위 객체는 생성이 된다.
<br>
이때 상위 객체의 메소드를 불러오면 아무값도 정의가 되지 않은채 호출이 되므로 NullPointerException 이 발생한다.
<br>
이를 해결할 방법이 필요한데 그것은 다음에 살펴볼 생성자를 통한 주입이다.

## 생성자 주입 ? 
```java
public interface TestInterface {
  void print (String text);
}

public class A implements TestInterface {

  @Override
  public void print (String text) {
    System.out.println("A: " + text);
  }

}

public class B implements TestInterface {

  @Override 
  public void print (String text) {
    System.out.pritln("B: " + text);
  }

}

public class MainClass ()  {

  private TestInterface testInterface;

  public MainClass (TestInterface testInterface)  {
    this.testInterface = testInterface;
  }

  public void printText () {
    TestInterface.print("Test");
  }

}

MainClass m = new MainClass(new A());
m.printText();
```
생성자를 통한 주입은 위 코드 내용처럼 생성자의 파라미터로 하위 객체를 넘겨주지 않는 이상 Null 에러는 발생할 수 없다.
<br>
추가로 final 을 사용할 수 있다. final 은 객체의 생성과 동시에 초기화가 되므로 setter 함수를 통해서는 초기화가 될 수 없다.
<br>
<br>
스프링에서는 **인터페이스를 통한 주입**, **생성자를 통한 주입** 외에도 **필드 인젝션**이라는 인터페이스를 통한 주입과 비슷한 동작으로 이루어지는 방법이 있다.
<br>
흔히 사용되는 @Autowired 로 불러오는 방식이다.

## 그러면 왜 인터페이스 주입 (수정자 (setter) 주입) 말고 생성자 주입을 추구해야되는건가 ?
스프링에서는 순환참조라는게 있다고 한다.
<br>
객체 A 의 메소드에서 객체 B 의 메소드를 호출하고 객체 B 의 메소드에서 객체 A 의 메소드를 호출하며 순환되는것인데,
<br>
인터페이스 주입 (수정자 주입) 의 경우 런타임 후에 비즈니스 로직상에서 주입이 이뤄지므로 stackoverflow 가 날때까지 계속 반복하게 된다.
<br>
반면 생성자 주입의 경우 런타임에서 객체 생성이 이뤄지기 때문에 이때 순환참조가 일어날 경우 곧바로 에러를 출력하며 실행이 되지 않는다.
<br>
이처럼 런타임이 순환참조임을 알리며 실행이 되지 않는 것과 아무것도 모르고 실행이 되어 오류를 뱉는 경우
<br>
어느 방법이 좀 더 정확한 에러를 파악하기에 쉬을것인가에 대해 생각해보면 생성자 주입을 추구하는게 맞는것 같다.

## 생성자 주입은 테스트 코드 작성에도 용이하다.
수정자 주입의 경우 스프링의 IOC 컨테이너 (제어의 역전 (의존성 역전)) 에서 객체를 생성해주는 방식이다.
<br>
그러므로 테스트 시 사용자가 필요한 객체를 생성하여 코드를 작성할 수 없다.
<br>
그래서 수정자 주입이 된 메소르를 단위 테스트를 해보면 NullPointerException 이 발생한다.
<br>
<br>
생성자 주입의 경우에는 객체를 생성해서 주입해주기 때문에 Null 에러가 나지 않고, 수월한 테스트가 가능하다.


필드 인젝션?
생성자 주입?
