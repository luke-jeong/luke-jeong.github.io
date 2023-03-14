---
layout: post
title:  "[Spring] Spring Basic 04"
date:   2023-03-14 09:50:00 +0900
categories: Spring
---
## Spring으로 전환
이전까지 작성한 코드를 Spring으로 전환해보았다.
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
     @Bean
     public DiscountPolicy discountPolicy() {
     		return new RateDiscountPolicy();
     }
}
```
AppConfig에 설정을 구성한다는 뜻의 @Configuration 을 붙여준다.
각 메서드에 @Bean 을 붙여준다. 이렇게 하면 스프링 컨테이너에 스프링 빈으로 등록한다.

```java
public class MemberApp {
   public static void main(String[] args) {
      // AppConfig appConfig = new AppConfig();
      // MemberService memberService = appConfig.memberService();
       ApplicationContext applicationContext = 
       		new AnnotationConfigApplicationContext(AppConfig.class);
       MemberService memberService =
      		applicationContext.getBean("memberService", MemberService.class);  
      
       Member member = new Member(1L, "memberA", Grade.VIP);
       memberService.join(member);
       Member findMember = memberService.findMember(1L);
       
       System.out.println("new member = " + member.getName());
       System.out.println("find Member = " + findMember.getName());
   }
}
```
* ApplicationContext 를 스프링 컨테이너라 한다.
* 기존에는 개발자가 AppConfig 를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 사용한다.
* 스프링 컨테이너는 @Configuration 이 붙은 AppConfig 를 설정(구성) 정보로 사용한다. 여기서 @Bean 이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다.
* 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다.
* 스프링 빈은 @Bean 이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다. ( ex - memberService ,orderService )
* 이전에는 개발자가 필요한 객체를 AppConfig 를 사용해서 직접 조회했지만, 이제부터는 스프링 컨테이너를 통해서 필요한 스프링 빈(객체)를 찾아야 한다.
* 스프링 빈은 applicationContext.getBean() 메서드를 사용해서 찾을 수 있다.

## BeanFactory와 ApplicationContext
![](https://velog.velcdn.com/images/ghjeong/post/1fd8f804-0eff-408a-932e-55ef7d4dcbb7/image.png)

* BeanFactory
    * 스프링 컨테이너의 최상위 인터페이스다.
    * 스프링 빈을 관리하고 조회하는 역할을 담당한다.
    * getBean() 을 제공한다.
    * 지금까지 우리가 사용했던 대부분의 기능은 BeanFactory가 제공하는 기능이다.
* ApplicationContext
    * BeanFactory 기능을 모두 상속받아서 제공한다.
    * 빈을 관리하고 검색하는 기능을 BeanFactory가 제공해주는데, 그러면 둘의 차이가 뭘까?
    * 애플리케이션을 개발할 때는 빈을 관리하고 조회하는 기능은 물론이고, 수 많은 부가기능이 필요하다.  
      ![](https://velog.velcdn.com/images/ghjeong/post/bd755649-b42c-47c1-8300-42aadb3074b8/image.png)

    * ApplicationContext는 BeanFactory 뿐 아니라 다른 부가기능도 가지고있다.
        * 메시지소스를 활용한 국제화 기능
        * 환경변수 - 로컬, 개발, 운영등을 구분
        * 애플리케이션 이벤트 - 이벤트 구독, 발행하는 모델 지원
        * 편리한 리소스 조회

> 결론
> * ApplicationContext는 BeanFactory의 기능을 상속받는다.
> * ApplicationContext는 빈 관리기능 + 편리한 부가 기능을 제공한다.
> * BeanFactory를 직접 사용할 일은 거의 없다. 부가기능이 포함된 ApplicationContext를 사용한다.
> * BeanFactory나 ApplicationContext를 **스프링 컨테이너**라 한다

## 스프링 빈 설정 메타정보 - BeanDefinition
BeanDefinition에 대해서는 너무 깊이있게 이해하기 보다는, 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 된다. 
