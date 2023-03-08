---
layout: post
title:  "[Java] JVM Memory Structure"
date:   2023-03-05 18:06:00 +0900
categories: java
---

지난번 GC에 대해 알아보다가 JVM 메모리 구조에 대해 궁금해져 찾아봤다.

자바가상머신(JVM)의 동작방식은 아래와 같다.  
![](https://velog.velcdn.com/images/ghjeong/post/2114d90f-84f9-4e6d-975a-affba55215b3/image.png)
1. 자바로 개발된 프로그램이 실행되면 JVM은 OS로부터 메모리를 할당한다.
2. 자바 컴파일러(javac)가 자바 소스코드(.java)를 자바 바이트코드(.class)로 컴파일한다.
3. Class Loader를 통해 JVM Runtime Data Area로 로딩한다.
4. Runtime Data Areadp fheldehls .class 들은 Execution Engine을 통해 해석한다.
5. 해석된 바이트 코드는 Runtime Data Area (**Memory Area**) 의 각 영역에 배치되어 수행하며 이 과정에서 Execution Engine에 의해 GC의 작동과 스레드 동기화가 이루어진다.

## 구조
1. Class Loader  
   자바는 동적으로 클래스를 읽어오므로, 프로그램이 실행중인 런타임에서야 모든 코드가 JVM과 연결된다. 이렇게 동적으로 클래스를 로딩해주는 역할을 하는 것이 **클래스 로더**이다. .java 소스를 컴파일러가 컴파일하면 .class파일이 생성되는데, 클래스 로더는 .class파일을 묶어서 JVM이 운영체제로부터 할당받은 메모리 영역인 Runtime Data Area로 적재한다.  
   ![](https://velog.velcdn.com/images/ghjeong/post/fcef4c5a-114f-45d0-a8e4-1ede29b2f3d7/image.png)


2. Execution Engine  
   클래스 로더에 의해 JVM으로 로드 된 .class 파일들은 Runtime Data Area의 Method Area에 배치되는데, 배치된 이후에 JVM은 Method Area의 바이트코드를 **실행엔진**에 제공하여 정의된 내용대로 바이트 코드를 실행시킨다. 이때, 로드된 바이트코드를 실행하는 런타임 모듈이 실행엔진이다. 실행엔진은 바이트코드를 명령어 단위로 읽어서 실행한다.

3. Garbage Collector  
   가비지 컬렉터는 지난 글에 설명한 적이 있다. JVM은 GC를 이용하여 더 이상 사용하지 않는 메모리를 자동으로 회수해준다. Heap 메모리 영역에 생성된 객체들 중에 참조되지 않는 객체들을 탐색 후 제거한다. GC역할을 수행하는 스레드를 제외한 나머지 모든 스레드는 일시정지상태가 된다.  
![](https://velog.velcdn.com/images/ghjeong/post/3c63ff80-da1d-44e6-9c4e-82f4373b01e6/image.png)



## Memory Area
![](https://velog.velcdn.com/images/ghjeong/post/5883ad66-79a2-4270-850e-eb9e491f083a/image.png)

1. Method Area  
   JVM이 실행되면서 생기는 공간.  
   Class 정보, 전역변수 정보, static 변수 정보가 저장되는 공간이다. Runtime Constant Pool에는 상수 정보가 저장된다.  
   모든 스레드에서 정보가 공유된다.
2. Heap  
   new 연산자로 생성된 객체, Array와 같은 동적으로 생성된 데이터가 저장되는 공간.  
   Heap에 저장된 데이터는 GC가 처리하지 않는 한 소멸되지 않는다. Reference Type의 데이터가 저장된다.  
   모든 스레드에서 정보가 공유된다.
3. Stack  
   지역변수, 메소드의 매개변수와 같이 잠시 사용되고 필요없어지는 데이터가 저장되는 공간.  
   지역변수이지만 Reference Type일 경우에는 Heap에 저장된 데이터의 주소값을 Stack에 저장해서 사용하게된다.  
   스레드마다 하나씩 존재.
4. PC Register  
   스레드가 생성되면서 생기는 공간.  
   스레드가 어느 명령어를 처리하고 있는 지 그 주소를 등록한다.  
   JVM이 실행하고 있는 현재 위치를 저장한다.
5. Native Method Stacks  
   JAVA가 아닌 다른 언어로 구성된 메소드를 실행시 사용되는 공간.


> 참고
- https://coding-factory.tistory.com/828
- https://velog.io/@shin_stealer/%EC%9E%90%EB%B0%94%EC%9D%98-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B5%AC%EC%A1%B0
- https://steady-coding.tistory.com/305
