---
layout: post
title:  "[Spring] Spring Basic 06"
date:   2023-03-15 16:05:00 +0900
categories: Spring
---
## ComponentScan
지금까지 스프링 빈을 등록할 때는 자바 코드의 @Bean이나 XML의 &ltbean&gt 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다.

지금까지 예제에서는 몇개가 안되었지만, 이렇게 등록해야 할 스프링 빈이 수십, 수백개가 되면 일일이 등록하기도 귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생한다  
그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔(ComponentScan)이라는 기능을 제공한다
```java
@Configuration
@ComponentScan
public class AutoAppConfig {
 
}
```
이렇게 등록하면 AppConfig 내에 일일히 빈을 등록할 필요 없이, @Component 가 붙은 클래스를 자동으로 스프링 빈으로 등록해준다.  
SrpingBoot에서는 @SpringBootApplication안에 자동으로 ComponentScan이 들어가있어, 해당 클래스가 포함된 폴더 이하를 모두 스캔한다.  

![](https://velog.velcdn.com/images/ghjeong/post/2e5dff05-8f7d-4a8d-8b27-9fb75038efd4/image.png)
* 이때 스프링 빈의 기본 이름은 클래스 명을 사용하되 맨 앞글자만 소문자를 사용한다.
* 직접 지정하고싶으면 @Component("memberService2") 이런식으로 이름을 부여하면 된다.


```java
@Component
public class MemberServiceImpl implements MemberService {
   private final MemberRepository memberRepository;
   
   @Autowired
   public MemberServiceImpl(MemberRepository memberRepository) {
   		this.memberRepository = memberRepository;
   }
}
```
이전 AppConfig에선 @Bean으로 직접 설정정보를 작성하고 의존관계도 명시했지만, ComponentScan에서는 의존관계를 명시하지 않기 때문에, 의존관계 주입을 해당 클래스 안에서 해결애야한다.  
이때 사용하는 것이 **@Autowired** 이다.

### ComponentScan 대상
컴포넌트 스캔은 @Component 뿐만 아니라 다음과 내용도 추가로 대상에 포함한다.
* @Component : 컴포넌트 스캔에서 사용
* @Controlller : 스프링 MVC 컨트롤러에서 사용
* @Service : 스프링 비즈니스 로직에서 사용
* @Repository : 스프링 데이터 접근 계층에서 사용
* @Configuration : 스프링 설정 정보에서 사용

컴포넌트 스캔의 용도 뿐만 아니라 다음 애노테이션이 있으면 스프링은 부가 기능을 수행한다.
* @Controller : 스프링 MVC 컨트롤러로 인식
* @Service : 사실 @Service 는 특별한 처리를 하지 않는다. 대신 개발자들이 비즈니스 계층을 인식하는데 도움이 된다
* @Repository : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환해준다.
* @Configuration : 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.

### 빈 등록 충돌
* 수동 빈 vs 자동 빈 의 경우  
  수동 빈 등록이 우선권을 가진다.

* 자동 vs 자동의 경우  
  컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킨다.
  ConflictingBeanDefinitionException 예외 발생