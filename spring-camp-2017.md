## 목차
- [프로세스와 스레드, NIO 그리고 리액티브 스트림](#프로세스와-스레드,-NIO-그리고-리액티브-스트림)
- [Async & Spring](#Async-&-Spring)
- [Spring WebFlux](#Spring-WebFlux)
- [비동기 어플리케이션, 어떻게 모니터링 할 것인가?](#비동기-어플리케이션,-어떻게-모니터링-할-것인가?)
- [Reactive Spring (Spring 5 & Reactor)](#Reactive-Spring-(Spring-5-&-Reactor))
- [이벤트 소싱 소개](#이벤트-소싱-소개)

# 프로세스와 스레드, NIO 그리고 리액티브 스트림

### 프로세스
- 독립적인 실행단위
cpu : os 스케줄러에 의해 time slice 만큼 실행
memory : os에 의해 관리


### 쓰레드 일반

프로그램 -> 프로세스

cpu : 가상메모리 + 프로세서 (p1, p2, p3)


프로세스 :
  - 커널영역 : T1 (task_struct)
  - 유저영역 : Stack, Heap, data, code

신규스레드가 사용할 stack memory 할당.

### Context Switch
- cpu register 상태 저장, 복원 : cpu 자원을 잠시 쓸 수 없는 상황


### JVM
jvm internal

### 비동기가 필요한 상황
cpu expesive
병렬화 시간단축

io blocking
대기하면 스레드 자원 낭비

### CompletableFuture

```java
cf.complete(args)
cf.get()

CompleteFuture
.completedFuture()
.thenApply()
.themApply()
.get()


cf1
.thenCombine()
.thenCombine()
.get()
```

컨텍스트변경
.thenApplyAsync

반복되는 작업을 Collection에 넣고, 이를 CompletableFuture 처럼 처리
==> 토비

### nio
native io
nonblocking io : java.nio.channels : selector, channel, handler (io 작업을 단일 스레드로 처리)

###

deferredResult

event  발생 -> 처리

Source -> Event -> listener Handler

### backpressure

back pressure valve

circuit break pattern 개념과 비슷

데이터 스트림 처리시.. 데이터가 들어오면 흘려 보내고, 없으면 흘려 보내지 않고...

boojongmin

# Async & Spring

무엇과 무엇이? 어떤 시간?

동기/비동기 : 대상과 시간을 두고 설명을 해야 함

무엇과 무엇이 동기? 비동기?
무엇이 블로킹? 논블로킹?
분리하여 생각해야 함!!!

java 에서 블로킹이 발생하면, context switching 두번 발생 (호출시, 응답시)

비동기 지만 논블로킹 IO를 사용하지 않는다.
Thread 를 순간적으로 늘려서 처리 한다.

netty를 사용하여, thread 1개 는블로킹 io 를 이용하여 처리 함.

CompletableFuture : 비동기 작업 끝! 결과 줄께.

# Spring WebFlux

==> 기존 spring @mvc 기능과 비교해서 이해 하면 좋을 듯


요청매핑    RouterFunction
요청바인딩
핸들러실행
핸들러결과 처리
          HandlerFunction

            웹응답
              http 응답 : 응답코드. 헤더. 바디
              ServerResponse 빌더 활용
              Mono에 담긴 ServerResponse 타입으로 리턴

Mono or Flux 타입으로 담아서 response 를 준다!!!

Method Reference의 특징을 알아볼것.

### 개설할 블로킹 IO
- 디비 : jdk 10 or Async 방식, CompletableFuture : spring-data 참조!!!
- Rest API
- 네트웤


### 리액티브 데이터 액세스 기술
- mongo, cassandra, redis : spring data 2.0 : ReactiveCrudRepository



### 이것 때문에 시도 할 만하다.
동기적으로 작성된 코드를 reactive 하게 실행 할 수 있다!!!
backpressure : 데이터의 흐름 속도 제어


# 비동기 어플리케이션, 어떻게 모니터링 할 것인가?


현대의 모니터링 : 원인에서 기인한 결과.

APM : 원인을 찾기위한 그 무언가.

bci 예제 : thread name enhancer : uri? start time? argument?

==> 시스템의 기본 아키텍쳐를 이용하여, 성능 context 관리.

ThreadLocal
- security context
- transaction context
- logging mdc

Thread.currentThread 를 key로 사용하여 Map 으로 관리

==> 비동기 어플리케이션과 모니터링 ?

==> context 가 전파 가능 해야 한다.

요청과 응답 thread가 다르다.

==> InheritableThreadLocal : 보통 thread 는 thread pool 사용되기 때문에 언제 생성 될지, 그리고 재활용 문제로 사용 불가.

lambda transform 이 수행 안된다..

==> bytebuddy.net

*** security context 를 살펴 보면, context  비동기 전파 insight를 얻을 수 있다!!!

# Reactive Spring (Spring 5 & Reactor)


리소스 효율 ??

thread pool ???

IO Selector => Worker :: Pub Sub 개념 차용하여 설명
               (Queue)

cloud 를 생각해 보아도, 확장성과 고가용성 확보는 기존 방식으로 대응하는 것 이 아닌,
Reactive 디자인 패턴과 nio 기술을 이용하여 해결 하고자 함.

managing with RxJava

# 이벤트 소싱 소개

이벤트 소싱은 : 데이터 저장 기법이지, EDA 가 중심이 아니다.

상태 변화의 기록을 저장관리.
응용프로그램 진단 관리를 위한 상태 저장 관리.

상태, 로그 저장은 원자성을 가지지 않는다. :: 데이터 유실의 가능성을 가진다.

변화를 이벤트로 하여 이벤트 저장소에 저장하고, 이 저장들을 이용한 이벤트 핸들러가 최종 상태 관리

모든 사실을 기록하는 패턴

영속데상은 이벤트이고, 그 이벤트는 오직 추가만 되지, 삭제되거나 변경 되지 않는다.

### 데이터 영속
이벤트 저장소는 수많은 이벤트 스트림으로 구성된다. 그 단위는 aggregate 이다. 즉 도메인 오브젝트 하나당 스트림 생성

커멘드
- 검증대상
- 실패 가능성이 존재
- 명령형 동사를 이용하여 네이밍

이벤트
- 이미 벌어진 돌이킬수 없는 사실로서 검증 대상이 아니다.
- 이벤트 재생시 항상 성공
- 이벤트 는 과거형 동사로 네이밍

### 도메인 오브젝트 복원
- 개체 상태는 초기값과 버전 1~n 을 순차적으로 집계한 그 결과
- 특정 버젼의 상태를 재현하면 그 버젼의 이벤트까지만 재현 하면 됨
- 문제점 발생시. 현재까지의 변경 이력이 저장되므로, 언제든지 과거 특정 시점을 복원 가능

### 이벤트 생성과 복원
명령 , 명령 처리기 (이벤트 발생), 이벤트 핸들러, 이벤트 저장소로 저장
이벤트 저장소ㅓ, 이벤트 복원, 이벤트 핸들러로 상태 복원

### 이벤트 스키마
키와 값으로 구성
키는 개체 식별자와 버젼
값은 이벤트 타입과 완정한 정보의 직렬화 및 추가정보 관리 (command id 등...)

### 스키마 프리.. orm 인피던스 불일치 해결

### 복합키로 구성. object id + version

### 백만개의 이벤트를 가지는 도매인 개체
스냅샷 사용.
특정 이벤트 및 상황마다 갱신한다.
개체 보관 성능을 높인다.

### 스냅샷 스키마
키는 개체 아이디,
값은 버전과 직렬화 상태

### 매시징
정확히 한번 최대 한번, 최소 한번 보낼 것!!! ==> 메시지 유실 가능성을 감안한 디자인
2pc 는 사용하지 않는다.
비즈니스 로직에는 사용하지 않는다.
메시지의 멱등성 확보해야 함. ==> 과거 상태와 현재값 기대값을 고려 해야 함

이벤트스토어에 저장되지 않는다면, 그 정보는 갱신되지 않은 것이다.

### 이벤트 스트림의 순서 보장

### CQRS
cqs : 명령 조회 분리
질문은 대답을 변경하지 않음

- 메소드 이상의 개념
개체 자체의 책임을 분리
시스템 구분, 어플리케이션 구분

구체화된 뷰, 조회에 최적화된 뷰, materialized view 테이블 구성, 정규화도 안한다.

### 고려사항
