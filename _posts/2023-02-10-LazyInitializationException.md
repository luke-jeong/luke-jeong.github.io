---
layout: post
title:  "LazyInitializationException"
date:   2023-02-10 18:00:00 +0900
categories: JPA
---

지난번 proxy 정리 글에서 나왔던 `LazyInitializationException`에 대해 정리하려고 한다.

> LazyInitializationException 는 영속성 컨텍스트에 있던 Entity가 준영속(Detached)상태가 되었을때 Lazy Loading을 실행해서 영속성 컨텍스트에서 해당 객체를 조회하려고 했을때 발생하는 예외이다.

FetchType.LAZY가 설정된 필드가 포함된 Entity 오브젝트에 대해 컨트롤러 레벨에서 해당 필드를 조회할때 Getter 매서드를 호출하고 실제 조회 쿼리가 실행된다.

서비스 레벨에서 Transactional이 명시된 매서드가 종료되면 Hibernate의 Session도 함께 종료되므로 이미 종료된 Session으로 인해 `LazyInitializationException`가 발생하게 된다.


Springboot에서 기본값으로 OSIV를 활성상태로 설정

> Open Session In View(OSIV)는 지난 proxy 글에서도 잠깐 설명했듯, 영속성 컨텍스트를 뷰 렌더링 완료 시점까지 열어두는 기능을 말한다. 영속성 컨텍스트가 유지되면 엔티티도 영속 상태로 유지.

OSIV상태에서는 Controller의 `Hibernate Session`과 `Persistence Context`가 열려있게 된다.



>참고
- https://velog.io/@oenomel87/JPA-LazyInitializationException
- https://jsonobject.tistory.com/605
- https://xzio.tistory.com/2052
