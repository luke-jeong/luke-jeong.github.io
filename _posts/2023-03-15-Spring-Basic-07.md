---
layout: post
title:  "[Spring] Spring Basic 07"
date:   2023-03-15 16:07:00 +0900
categories: Spring
---
## 의존관계 주입
의존관계 주입은 크게 4가지 방법이 있다.
* 생성자 주입
* 수정자 주입(setter 주입)
* 필드 주입
* 일반 메서드 주입

### 1. 생성자 주입
이름 그대로 생성자를 통해서 의존 관계를 주입 받는 방법이다.
* 특징
    * 생성자 호출 시점에 딱 1번만 호출되는것이 보장됨.
    * 불변, 필수 의존관계에 사용한다.

```java
@Component
public class OrderServiceImpl implements OrderService {
   private final MemberRepository memberRepository;
   private final DiscountPolicy discountPolicy;
   
   @Autowired
   public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy 
  discountPolicy) {
       this.memberRepository = memberRepository;
       this.discountPolicy = discountPolicy;
   }
}
```
>생성자가 위처럼 딱 1개만 있으면 @Autowired 생략해도 자동주입 됨

### 2. 수정자 주입(setter 주입)
setter라 불리는 필드의 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입하는 방법이다.
* 특징
    * 선택, 변경 가능성이 있는 의존관계에 사용
    * 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법이다.

```java
Component
public class OrderServiceImpl implements OrderService {
   private MemberRepository memberRepository;
   private DiscountPolicy discountPolicy;
   
   @Autowired
   public void setMemberRepository(MemberRepository memberRepository) {
   		this.memberRepository = memberRepository;
   }
   
   @Autowired
   public void setDiscountPolicy(DiscountPolicy discountPolicy) {
   		this.discountPolicy = discountPolicy;
   }
}
```
> @Autowired 의 기본 동작은 주입할 대상이 없으면 오류가 발생한다. 주입할 대상이 없어도 동작하게 하려면 @Autowired(required = false) 로 지정하면 된다.

### 3. 필드 주입
* 특징
    * 코드가 간결해서 많은 개발자들을 유혹하지만 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있다.
    * DI 프레임워크가 없으면 아무것도 할 수 없다.
    * 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도로 사용하기 때문에 평소에는 잘 쓰이지 않음.
```java
@Component
public class OrderServiceImpl implements OrderService {
   @Autowired
   private MemberRepository memberRepository;
   
   @Autowired
   private DiscountPolicy discountPolicy;
}
```
> 순수한 자바 테스트 코드에서는 @Autowired가 동작하지 않는다.
> @SpringBootTest 처럼 스프링 컨테이너를 테스트에 통합한 경우에만 가능하다.

### 4. 일반 메서드 주입
일반 메서드를 통해서 주입 받을 수 있다.
* 특징
    * 한번에 여러 필드를 주입 받을 수 있다.
    * 일반적으로 잘 사용하지 않는다
```java
@Component
public class OrderServiceImpl implements OrderService {
   private MemberRepository memberRepository;
   private DiscountPolicy discountPolicy;
   
   @Autowired
   public void init(MemberRepository memberRepository, DiscountPolicy 
  discountPolicy) {
       this.memberRepository = memberRepository;
       this.discountPolicy = discountPolicy;
   }
}
```
> 스프링 빈이 아닌 Member 같은 클래스에서 @Autowired 코드를 적용해도 아무 기능도 동작하지 않는다. 의존관계 자동 주입은 스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다

### 옵션 처리
주입할 스프링 빈이 없어도 동작해야 할 때가 있다.  
아래 3가지 방법으로 자동 주입 대상 처리 방법을 결정할 수 있다.
* Autowired(required=false) : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨
* org.springframework.lang.@Nullable : 자동 주입할 대상이 없으면 null이 입력된다.
* Optional<> : 자동 주입할 대상이 없으면 Optional.empty 가 입력된다.

```java
@Autowired(required = false)
public void setNoBean1(Member member) {
 System.out.println("setNoBean1 = " + member);
}
//null 호출
@Autowired
public void setNoBean2(@Nullable Member member) {
 System.out.println("setNoBean2 = " + member);
}
//Optional.empty 호출
@Autowired(required = false)
public void setNoBean3(Optional<Member> member) {
 System.out.println("setNoBean3 = " + member);
}
```