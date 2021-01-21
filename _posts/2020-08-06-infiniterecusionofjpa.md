---
title: "JPA 무한 순환참조 Stack over flow 오류"
date: 2020-08-06
categories: posts
layout: single
classes: wide
tags: ["jpa"]
---

### **Json 순환참조**
---
JPA 를 사용하면서 Team -> Member 와 같이 OneToMany -> ManyToOne 과 같은 양방향 관계를 가지는 구조에서 많이 발생하는 Json 순환참조에 대해서 알아보자.

Json 순환참조는 양뱡향 관계에서 객체를 직렬화 하는 과정에서 양쪽 모두 직렬화 하려할때 무한참조가 되어 StackOverFlow 를 발생시킨다. 

무심코 Lombok 을 사용하며 @Data 를 사용하거나 @ToString 및 hashcode&equals 어노테이션을 사용하면서 발생하기도 한다. ToString 이나 HashCode&Equals 에서 양 객체가 서로의 프로퍼티를 계속해서 참조하면서 무한 순환참조가 발생한다.

즉, 가급적이면 Entity 에서 @Data, @ToString 등의 어노테이션 사용을 지양하자.

다음은 몇가지 순환참조 해결방법이다.

1. **@JsonIgnore**
2. **ToString Override**
3. **@JsonManagedReference, @JsonBackReference**
4. **@JsonIdentityInfo**

첫번째 **@JsonIgnore** 의 경우 해당 어노테이션이 적용된 부분을 Json 직렬화에서 아예 제외시키므로 좋은 방법이 아니다.

두번째 역시 매번 새로 Override 를 해줘야 하기 떄문에 생산성 측면에서 좋은 방법이 아니다. 그러면 남은 방법은 3, 4번째다. 3 번째는 Jackson 라이브러리 1.x 버전에서 4 번째는 2.x 버전 이상에서 사용가능하다.

3번째의 경우 예를 들어보겠다. 다음 예시의 Entity 두개를 보자.

```java
@Entity
public class User implements Serializable{
 
 @Id
 @GeneratedValue(strategy=GenerationType.IDENTITY)
 private long id;
 
 @Column(name="name")
 private String name;

 @ManyToMany
 @JoinTable(name="users_roles",joinColumns=@JoinColumn(name = "user_fk"),
 inverseJoinColumns=@JoinColumn(name = "role_fk"))
 @JsonManagedReference
 private Set<Role> roles = new HashSet<Role>();

...

@Entity
public class Role implements Serializable {

 @Id 
 @GeneratedValue(strategy=GenerationType.IDENTITY)
 private int id;

 @ManyToMany(mappedBy="roles")
 @JsonBackReference
 private Set<User> users = new HashSet<User>();

...
```

여러명의 User 가 여러개의 Role 을 갖는 양방향 다대다 구조이다. 양방향 구조이기 때문에 무한 순환참조가 발생할 것이다. User 에 있는 *Set<Role> roles = new HashSet<Role>();* 부분이 Jackson 에서 가져오는 **Forward part of Reference**. 즉, Json 형식으로 변환하는 부분이다. 이렇게 앞에서 참조하는 부분을 가져와 Json 으로 변환하는 것을 마샬링 프로세스라고 한다.

그리고 Role Entity 에서 *Set<User> users = new HashSet<User>();* 부분은 **Backward part of Reference** 라고 뒤에서 참조되는 부분이다. Jackson 이 부분을 직렬화하지 않고 그냥 두어야 무한 순환참조가 발생하지 않게 된다. 

그래서 주인 Entity 인 User, JoinColumn 이 선언된 곳에서는 Json 참조를 발생시키고, 자식 Entity 인 Role, MappedBy 가 선언된 곳에서는 Json 직렬화를 방지하게 된다.

마지막으로 **@JsonIdentityInfo** 에 대해서 간단한 사용방법을 소개하고 마치겠다.

```java
@Entity
@JsonIdentityInfo(generator = IntSequenceGenerator::class, property = "id")
public class User implements Serializable{
 
 @Id
 @GeneratedValue(strategy=GenerationType.IDENTITY)
 private long id;
 
 @Column(name="name")
 private String name;

 @ManyToMany
 @JoinTable(name="users_roles",joinColumns=@JoinColumn(name = "user_fk"),
 inverseJoinColumns=@JoinColumn(name = "role_fk"))
 private Set<Role> roles = new HashSet<Role>();

...

@Entity
@JsonIdentityInfo(generator = IntSequenceGenerator::class, property = "id")
public class Role implements Serializable {

 @Id 
 @GeneratedValue(strategy=GenerationType.IDENTITY)
 private int id;

 @ManyToMany(mappedBy="roles")
 private Set<User> users = new HashSet<User>();

...
```

사용법은 간단하다. 각 Entity 에 *@JsonIdentityInfo(generator = IntSequenceGenerator::class, property = "id")* 를 추가하면된다. generator 에는 정수형 ID 를 설정하는 **IntSequenceGenerator** 와 문자열 ID 를 설정하는 **StringGenerator** 와 UUID 형태로 설정하는 **UUIDGenerator** 등이 있다. 또한 property 속성에 별다른 값을 정하지 않으면 직렬화 할때 **@id** 라는 속성이름으로 객체의 Id 가 설정된다. 

<br>

## **마치며**
--- 

차근차근 공부하며 사용하는 JPA 인데 어려움이 많다. 그래도 많이 사용하고들 있어서인지 공부할 자료도 많고 문제해결 자료도 많아 도움이 많이 된다.

<br>

## **참고**
---
[Json and Jackson Stackoverflow 문제](https://keenformatics.blogspot.com/2013/08/how-to-solve-json-infinite-recursion.html)


[Infinite Recursion with Jackson JSON and Hibernate JPA issue](https://stackoverflow.com/questions/3325387/infinite-recursion-with-jackson-json-and-hibernate-jpa-issue)