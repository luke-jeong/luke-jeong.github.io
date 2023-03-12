---
layout: post
title:  "[Kafka] Kafka 활용"
date:   2023-03-12 11:21:00 +0900
categories: Kafka
---

MSA 프로젝트를 연습해보면서 [kafka를 적용하는 강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4/dashboard)를 들었다.

## Apache Kafka
Kafka는 Apache Software Foundation의 Scalar 언어로 된 오픈소스 메시지 브로커 프로젝트이다. Linked-in에서 개발되어, 2011년 오픈소스화 되었다. 실시간 데이터 피드를 관리하기 위해 통일된 높은 처리량, 낮은 지연시간을 지닌 플랫폼을 제공한다.

![](https://velog.velcdn.com/images/ghjeong/post/a034b453-9212-45aa-85ba-15f4117ecbbb/image.png)  
카프카와 같은 메시지 큐잉 시스템을 통해 복잡했던 데이터 전송 방식을 카프카 하나로 묶어 전달 할 수 있게 되었다.

## Kafka Broker
* 실행된 kafka application server를 말한다. 3대 이상의 Broker Cluster로 구성하는 것을 권장한다.
* 각각의 broker를 중재해주는 역할로 zookeeper를 사용한다. zookeeper는 메타데이터(Broker ID, Controller ID)를 저장하는 역할이다.
* n개 broker 중 1대는 Controller 기능을 수행한다. 여기서 Controller는 각 Broker에게 담당 파티션을 할당하고 Broker의 정상동작 모니터링을 한다.

## Producer / Consumer
powershell에서 zookeeper 실행 후 서버를 실행하고 테스트를 진행한다.

** Zookeeper 실행 **
> .\zookeeper-server-start.bat ..\..\config\zookeeper.properties


** Server 실행 **
> .\kafka-server-start.bat ..\..\config\server.properties

** Topic 등록 **
> .\kafka-topics.bat --bootstrap-server localhost:9092 --create --topic [topic이름]

** Producer 등록 **
>  .\kafka-console-producer.bat --broker-list localhost:9092 --topic [topic이름]

** Consumer 등록 **
> .\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic [topic이름] --from-beginning  
> 여기서  from-beginning은 consumer 연결 시 producer의 지난 모든 메시지를 모두 받아온다.

Producer와 Consumer는 직접적인 관련이 없다.  
Producer는 topic에 메시지를 전달하고 Consumer는 topic에 저장된 메시지를 받아오는 것 뿐이다. Consumer가 여러개가 되더라도 topic에서 메시지를 받아오기 때문에 producer에서 입력된 메시지를 동시에 받아올 수 있다.
