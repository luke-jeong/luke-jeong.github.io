---
layout: post
title:  "[Java] Java Garbage Collection"
date:   2023-02-26 14:22:00 +0900
categories: Java
---

가비지 컬렉션에 대해 알아봤다.
## Garbage Collection(GC)
가비지 컬렉션은 JVM의 Heap 영역에서 동적으로 할당했던 메모리 영역 중 필요 없게된 메모리 영역을 주기적으로 삭제하는 프로세스를 말한다. Java나 Kotlin을 이용해 개발을 하면 JVM의 가비지 컬렉터가 불필요한 메모리를 알아서 정리해주기 때문에 개발자가 메모리를 직접 해제해줄 필요가 없다.

* 장점
    * 개발자 입장에서 메모리 관리, 메모리 누수 문제를 완벽하게 관리하지 않고 개발에 집중할 수 있다.
* 단점
    * 개발자가 메모리가 언제 해제되는지 정확하게 알 수 없다.
    * GC가 동작하는 동안은 다른 동작을 멈추기 때문에 오버헤드가 발생한다.

## GC 대상
객체가 참조되고 있는 상태(Reachable)이 아닌 **객체가 참조되고 있지 않은 상태일때(Unreachable)**, GC의 대상이 된다.


> 참고
- https://mangkyu.tistory.com/118
- https://coding-factory.tistory.com/829
- https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC
