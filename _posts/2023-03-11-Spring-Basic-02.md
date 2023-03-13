---
layout: post
title:  "[Spring] Spring Basic 02"
date:   2023-03-11 23:14:00 +0900
categories: Spring
---

## DIP, OCP 예제
```java
public class OrderServiceImpl implements OrderService {
  // private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
   private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
 }
```
위의 코드만 보았을때, 역할과 구현을 분리하고, 다형성 활용, 인터페이스와 구현객체를 분리하는 등 OCP, DIP 같은 객체지향 설계원칙을 준수한 것으로 보인다.  
하지만 클래스 의존관계를 보면  
![](https://velog.velcdn.com/images/ghjeong/post/c1346895-f76f-4d94-b9f3-a235d52b9365/image.png)  
기대했던 의존관계와 달리,  
![](https://velog.velcdn.com/images/ghjeong/post/334bc764-d32f-4e08-ab4a-7a02acf6f6c6/image.png)  
**인터페이스**(DiscountPolicy) 뿐 아니라 **구현 클래스**(FixDiscountPolicy, RateDiscountPolicy)에도 의존하고 있으므로, **DIP 위반**이다.  
현재 코드대로라면 기능을 확장해서 변경할때, 클라이언트 코드에 영향을 주므로 **OCP를 위반**한다고 할 수 있다.

## 문제 해결 방법
간단하게 인터페이스에만 의존하도록 코드를 변경하면 된다.
```java
public class OrderServiceImpl implements OrderService {
 //private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
 private DiscountPolicy discountPolicy;
}
```
하지만 이대로면 구현객체가 없어 NullPointerException이 뜬다.   
따라서 외부에서 주입할 구현객체를 결정하도록 만들어야한다.
* AppConfig  
  AppConfig는 앞서 설명한 애플리케이션의 전체 동작 방식을 구성(Config)하기 위해, 구현객체를 생성하고, 연결하는 책임을 가지는 별도의 설정클래스이다.
 ```java
 public class AppConfig {
 public MemberService memberService() {
 	return new MemberServiceImpl(new MemoryMemberRepository());
 }
 public OrderService orderService() {
   return new OrderServiceImpl(
         new MemoryMemberRepository(),
         new FixDiscountPolicy());
 }
 ```
이 AppConfig에서 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)해준다.
  * MemberServiceImpl -> MemoryMemberRepositor
  * OrderServiceImpl -> MemoryMemberRepository , FixDiscountPolicy

```java
public class MemberServiceImpl implements MemberService {
   private final MemberRepository memberRepository;
   public MemberServiceImpl(MemberRepository memberRepository) {
   		this.memberRepository = memberRepository;
   }
 ```
* 설계 변경으로 MemberServiceImpl 은 MemoryMemberRepository 를 의존하지 않고, 단지 MemberRepository 인터페이스만 의존한다.
* MemberServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.
* MemberServiceImpl 의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부( AppConfig )에서 결정된다.
* MemberServiceImpl 은 이제부터 의존관계에 대한 고민은 외부에 맡기고 실행에만 집중하면 된다  
  ![](https://velog.velcdn.com/images/ghjeong/post/3869ed3c-75ef-4dc3-b361-9ac337fd4885/image.png)
  ![](https://velog.velcdn.com/images/ghjeong/post/b9bc88e0-6b35-4bc0-afbf-85b0914e254b/image.png)

* appConfig 객체는 memoryMemberRepository 객체를 생성하고 그 참조값을 memberServiceImpl을 생성하면서 생성자로 전달한다.
* 클라이언트인 memberServiceImpl 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것 같다고 해서
> DI (Dependency Injection)

우리말로 **의존관계 주입** 또는 **의존성 주입**이라 한다

## AppConfig 리팩터링
기존 AppConfig
```java
public class AppConfig {
   public MemberService memberService() {
   		return new MemberServiceImpl(new MemoryMemberRepository());
   }
   public OrderService orderService() {
   		return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
   }
}
```
리팩터링 진행한 AppConfig
```java
public class AppConfig {
   public MemberService memberService() {
   		return new MemberServiceImpl(memberRepository());
   }
   public OrderService orderService() {
   		return new OrderServiceImpl(memberRepository(), discountPolicy());
   }
   public MemberRepository memberRepository() {
   		return new MemoryMemberRepository();
   }
   public DiscountPolicy discountPolicy() {
   		return new FixDiscountPolicy();
   }
 }
```
중복을 제거하고 역할에 따른 구현이 보이도록 리팩터링했다. 역할을 세우고 그 안에 구현이 들어가게 해서 애플리케이션 전체 구성이 어떻게 되어있는지 빠르게 파악할 수 있다.

