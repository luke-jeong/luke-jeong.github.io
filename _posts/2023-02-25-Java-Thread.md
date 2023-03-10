---
layout: post
title:  "[Java] Java Thread"
date:   2023-02-25 16:27:00 +0900
categories: Java
---

스레드(Thread)는 자바에서 여러가지 일을 동시에 처리하기 위해 사용된다.
스레드를 설명하기 위해선 먼저 프로세스를 알아둘 필요가 있다.

## Process
프로세스는 일반적으로 cpu에 의해 메모리에 올려져 **실행중인 프로그램**을 말한다. 프로그램 실행을 위해 OS로부터 실행에 필요한 자원인 **메모리**를 할당받는다. 프로세스는 각각 독립된 메모리 영역(Code, Data, Stack, Heap)을 가진다. 
자바 JVM은 주로 하나의 프로세스로 실행되며 동시에 여러 작업을 수행하기 위해 멀티 스레드를 지원한다.  

![](https://velog.velcdn.com/images/ghjeong/post/fb6a3778-e4df-4307-abf3-6e8a1eaf76a7/image.png)  
(그림 출처 : https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html)
## Thread
스레드는 프로세스 안에서 실질적으로 **작업을 실행하는 단위**를 말한다. 프로세스 내에는 적어도 한개 이상의 스레드가 존재한다. 같은 프로세스 안에 스레드를 여러개 생성하면 Stack만 따로 할당받고 Code, Data, Heap 영역은 공유한다.  

![](https://velog.velcdn.com/images/ghjeong/post/6c4028cd-2df8-4eb7-bb4f-27b141b6df67/image.png)  
(그림 출처 : https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html)

## Java Thread
자바 스레드는 JVM이 운영체제의 역할을 한다. 자바에서는 프로세스가 존재하지 않고, 스레드만 존재한다. 자바 스레드는 JVM에 의해 스케줄되는 실행단위 코드블록을 말한다. 자바 스레드로 작동할 스레드 코드를 작성하고, 실행을 시작하도록 JVM에 요청하는 역할을 개발자가 하게된다.

## Multi Process, Multi Thread
- 멀티 프로세스
> 멀티 프로세싱은 하나의 응용 프로그램을 여러개의 프로세스로 구성해 각 프로세스가 하나의 작업을 처리하도록 하는것을 말한다.
    
   * 장점
    * 여러개의 자식 프로세스 중 하나의 프로세스에서 문제 발생 시 다른 자식 프로세스에 영향을 주지 않는다.
   * 단점
    * **Context Switching** 과정에서 캐쉬 메모리 초기화 등 무거운 작업이 진행되고 많은 시간이 소모되는 등의 오버헤드 발생
   	** 여기서 **Context Switching** 은 cpu에서 여러 프로세스를 돌아가면서 작업을 처리하는 과정을 말한다. 동작중인 프로세스가 대기하면서 해당 프로세스의 상태(Context)를 보관하고 대기하고있던 다음 순서의 프로세스가 동작하면서 이전에 보관했던 프로세스 상태를 복구하는 작업이다.
    * 프로세스 사이에서 공유하는 메모리가 없어 Context Switching 발생 시 캐시에 있는 모든 데이터를 리셋하고 다시 캐시정보 불어와야함.
   	* 프로세스 각각의 독립된 메모리 영역 할당으로 인해 프로세스 사이의 변수 공유 불가. 
   
    
    
- 멀티 스레드
> 멀티 스레딩은 하나의 응용프로그램을 여러개의 스레드로 구성하고 각 스레드로 하여금 하나의 작업을 처리하도록 하는 것을 말한다. 많은 운영체제들이 멀티 프로세싱을 지원하나, 멀티 스레딩을 기본으로 하고있다. 웹서버가 대표적인 멀티 스레드 응용프로그램이다.
	
   * 장점
  	 * 시스템 자원 소모 감소 - 프로세스를 생성하여 자원을 할당하는 시스템 콜이 줄어 자원을 효율적으로 관리할 수 있다.
     * 시스템 처리 비용 감소 - 스레드 사이의 작업량이 작이 Context Switching이 빠르다. 스레드 간 데이터를 주고 받는것이 간단해진다.
     * 프로그램 응답시간 단축 - 프로세스 내의 Stack 영역을 제외한 모든 메모리를 공유하기 때문에 통신부담 적음.   
   * 단점
   	* 디버깅이 까다롭다.
    * 단일 프로세스 시스템의 경우 효과 미비
    * 프로세스 밖에서 스레드 제어 불가.
    * 자원 공유 문제 발생.
    * 하나의 스레드에 문제 발생 시 전체 프로세스에 영향
    
    
## Reason
멀티 프로세스 대신 멀티 스레드를 사용하는 이유.
 * 프로그램 여러개 켜는 것보다 하나의 프로그램 안에서 여러 작업을 해결하는것이 효율적인 자원 관리에 유리하다.
 * 프로세스간의 전환속도보다 스레드간의 전환속도가 더 빠르다.

>참고
- https://devlog-wjdrbs96.tistory.com/145
- https://kadosholy.tistory.com/121
- https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html
