---
layout: post
title:  "[Spring] Spring Basic 05"
date:   2023-03-14 10:20:00 +0900
categories: Spring
---
## 싱글톤 패턴
클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
따라서 객체 인스턴스를 2개 이상 생성하지 못하도록 private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야한다.

싱글톤 패턴을 적용하면 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다. 하지만 싱글톤 패턴은 다음과 같은 수 많은 문제점들을 가지고 있다.

* 문제점
* 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
* 의존관계상 클라이언트가 구체 클래스에 의존한다. DIP를 위반한다.
* 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
* 테스트하기 어렵다.
* 내부 속성을 변경하거나 초기화 하기 어렵다.
* private 생성자로 자식 클래스를 만들기 어렵다.
* 결론적으로 유연성이 떨어진다.
* 안티패턴으로 불리기도 한다.

## Spring container
이러한 문제점을 해결하고 싱글톤 패턴을 적용시킨것이 Singleton Contaioner(Spring Container)이다.  
![](https://velog.velcdn.com/images/ghjeong/post/5427bdac-99a2-4a0f-aa9c-4b8af741621e/image.png)  
스프링 컨테이너 덕분에 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를
공유해서 효율적으로 재사용할 수 있다.

### 주의점
* 무상태(stateless)로 설계해야 한다.
    * 특정 클라이언트에 의존적인 필드가 있으면 안된다.
    * 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
    * 가급적 읽기만 가능해야 한다.
    * 필드 대신에 Java에서 공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

## @Configuration과 싱글톤
```java
@Configuration
public class AppConfig {
   @Bean
   public MemberService memberService() {
   		return new MemberServiceImpl(memberRepository());
   }
   @Bean
   public OrderService orderService() {
       return new OrderServiceImpl(
       memberRepository(),
       discountPolicy());
   }
   @Bean
   public MemberRepository memberRepository() {
   		return new MemoryMemberRepository();
   }
   ...
}
```
위 코드에서 memberRepository의 경우  
* MemberService 에서 1번  
* OrderService 에서 1번  
* memberRepository 에서 1번  

총 3번의 호출로 인해 각각 다른 memberRepository가 생성되기 때문에 싱글톤이 깨지는 것처럼 보인다.  
그러나 막상 테스트를 해보면 memberRepository 인스턴스는 모두 같은 인스턴스가 공유되어 사용된다.
### 이유
@Configuration을 적용하게 되면 해당 클래스를 상속받은 임의의 다른 클래스를 만들고 , 그 다른 클래스를 스프링 빈으로 등록한다.  
![](https://velog.velcdn.com/images/ghjeong/post/f6be51cc-8d61-4c68-86ea-78d7beafb278/image.png)  
이 임의의 다른 클래스가 싱글톤이 보장되도록 해준다.  
@Bean이 붙은 매서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어지는 덕에, 싱글톤이 보장되는 것이다.  
@Bean만 사용해도 스프링 빈으로 등록은 되지만, 싱글톤을 보장하지는 않는다. 따라서 **스프링 정보는 항상 @Configuration을 사용**해야한다.