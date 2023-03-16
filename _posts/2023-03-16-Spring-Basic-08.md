---
layout: post
title:  "[Spring] Spring Basic 08"
date:   2023-03-16 17:55:00 +0900
categories: Spring
---
# 생성자 주입을 선택하는 이유
## 1. 불변
* 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.(불변해야 한다.)
* 수정자 주입을 사용하면, setXxx 메서드를 public으로 열어두어야 한다.
  누군가 실수로 변경할 수 도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.
* 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없다. 따라서 불변하게 설계할 수 있다
## 2. 누락
* 생성자 주입을 사용하면 주입 데이터를 누락했을 때 컴파일 오류가 발생하므로, IDE에서 어떤 값을 필수로 주입해야 하는지 알 수 있다.

## 3. final 키워드 사용
* 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.
* 앞서 설명한 주입 방식 중, 오직 **생성자 주입 방식**만 final 키워드를 사용할 수 있다.

> 정리
> * 생성자 주입 방식을 선택하는 이유는 여러가지가 있지만, 프레임워크에 의존하지 않고, 순수한 자바 언어의 특징을 잘 살리는 방법이기도 하다.
    기본으로 생성자 주입을 사용하고, 필수 값이 아닌 경우에는 수정자 주입 방식을 옵션으로 부여하면 된다.
    생성자 주입과 수정자 주입을 동시에 사용할 수 있다.
    항상 생성자 주입을 선택해라! 그리고 가끔 옵션이 필요하면 수정자 주입을 선택해라. 필드 주입은 사용하지
    않는게 좋다

# Lombok
막상 개발을 해보면, 대부분이 다 불변이고, 그래서 다음과 같이 필드에 final 키워드를 사용하게 된다.
```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
   private final MemberRepository memberRepository;
   private final DiscountPolicy discountPolicy;
}
```
@RequiredArgsConstructor를 사용하면 자동으로 의존관계 주입을 해준다.

## @Autowired, @Qualifier, @Primary
조회 대상 빈이 2개 이상일때 해결방법
* @Autowired 필드 명 매칭
* @Qualifier @Qualifier끼리 매칭 빈 이름 매칭
* @Primary 사용

### @Autowired 필드 명 매칭
@Autowired 는 타입 매칭을 시도하고, 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭한다

```java
// 필드 명을 빈 이름으로 변경
Autowired
private DiscountPolicy rateDiscountPolicy
```
필드 명이 rateDiscountPolicy 이므로 정상 주입된다.
필드 명 매칭은 먼저 타입 매칭을 시도 하고 그 결과에 여러 빈이 있을 때 추가로 동작하는 기능이다
> @Autowired 매칭 정리
> 1. 타입 매칭
> 2. 타입 매칭의 결과가 2개 이상일 때 필드 명, 파라미터 명으로 빈 이름 매칭

### @Qualifier 사용
@Qualifier 는 추가 구분자를 붙여주는 방법이다. 주입시 추가적인 방법을 제공하는 것이지 빈 이름을 변경하는 것은 아니다.
```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
```
```java
//생성자 자동주입 예시
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
 	@Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
}
```
@Qualifier 로 주입할 때 @Qualifier("mainDiscountPolicy") 를 못찾으면 어떻게 될까? 그러면 mainDiscountPolicy라는 이름의 스프링 빈을 추가로 찾는다. 하지만 @Qualifier 는 @Qualifier 를 찾는 용도로만 사용하는게 명확하고 좋다.
> @Qualifier 정리
> 1. @Qualifier끼리 매칭
> 2. 1번이 없으면 -> 빈 이름 매칭
> 3. 2번이 없으면 -> NoSuchBeanDefinitionException 예외 발생

### @Primary 사용
@Primary 는 우선순위를 정하는 방법이다. @Autowired 시에 여러 빈이 매칭되면 @Primary 가 우선권을 가진다.
```java
//rateDiscountPolicy가 우선권을 가진다.
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

### @Primary, @Qualifier 활용
코드에서 자주 사용하는 **메인 데이터베이스**의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 **서브 데이터베이스**의 커넥션을 획득하는 스프링 빈이 있다고 생각해보자. **`메인 데이터베이스`** 의 커넥션을 획득하는 스프링 빈은 **`@Primary 를 적용`** 해서 조회하는 곳에서 @Qualifier 지정 없이 편리하게 조회하고, **`서브 데이터베이스`** 커넥션 빈을 획득할 때는 **`@Qualifier 를 지정`** 해서 명시적으로 획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있다. 물론 이때 메인 데이터베이스의 스프링 빈을 등록할 때 @Qualifier 를 지정해주는 것은 상관없다
> 우선순위는 Qualifier가 더 높다.