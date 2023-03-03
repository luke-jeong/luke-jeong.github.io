---
layout: post
title:  "Java Garbage Collection"
date:   2023-03-03 18:12:00 +0900
categories: java
---

지난번 GC에 대해 알아보다가 JVM 메모리 구조에 대해 궁금해져 찾아봤다.

일단 자바가상머신(JVM)의 동작방식은 아래와 같다.
![](https://velog.velcdn.com/images/ghjeong/post/2114d90f-84f9-4e6d-975a-affba55215b3/image.png)
1. 자바로 개발된 프로그램이 실행되면 JVM은 OS로부터 메모리를 할당한다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트코드(.class)로 컴파일한다.
3. Class Loader를 통해 JVM Runtime Data Area로 로딩한다.
4. Runtime Data Areadp fheldehls .class 들은 Execution Engine을 통해 해석한다.
5. 해석된 바이트 코드는 Runtime Data Area의 각 영역에 배치되어 수행하며 이 과정에서 Execution Engine에 의해 GC의 작동과 스레드 동기화가 이루어진다.

-작성중-

> 참조
- https://coding-factory.tistory.com/828
- https://velog.io/@shin_stealer/%EC%9E%90%EB%B0%94%EC%9D%98-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B5%AC%EC%A1%B0
- https://steady-coding.tistory.com/305