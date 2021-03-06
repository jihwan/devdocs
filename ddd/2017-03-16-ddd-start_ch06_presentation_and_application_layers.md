---
layout: post
title: "ddd-start ch06 응용 서비스와 표현 영역"
description: "ddd-start ch06 응용 서비스와 표현 영역"
category: DDD
tags: [DDD]
---
{% include JB/setup %}

# 응용 서비스와 표현 영역

## 표현 영역과 응용 영역

사용자에게 기능을 제공하려면 도메인과 사용자를 연결해 줄 표현,응용 영역이 필요

표현영역은 사용자의 요청을 해석. (값받기, 값변환, 값검증, 서비스호출, 뷰선택, Exception 처리)

응용영역에 위치한 서비스는 실제 사용자가 원하는 기능을 제공

표현영역이 사용자로 부터 절달받은 데이터 형식과 용용서비스가 요구하는 메소드 파라미터가 일치하지 않기 때문에 표현영역은 응용서비스가 요구하는 형식으로 사용자 요청을 변환한다.

표현영역이 사용자와의 상호작용을 처리하기 때문에 응용 서비스는 표현영역에 의존하지 않는다. 응용영역은 기능 실행에 필요한 입력값을 전달 받고 실행 결과만 리턴한다.

> 요구사항 분석과 설계를 통해 사용자에게 제공해야 할 응용 서비스를 작성. 도메인에 대한 이해를 높이면서 도메인 계층의 컴퍼넌트 설계 및 구현. 사용자와의 상호 작용을 위해 표현영역을 구현.

## 응용 서비스의 역할

사용자가 요청한 기능을 실행.
Repository로 부터 도메인 객체를 구하고, 도메인 객체를 사용한다.

응용 서비스의 주요 역할은 표현영역에서 보자면, 도메인 영역과 연결해 주는 FACADE 역할을 한다.

도메인 객체 간의 흐름 제어만 한다. 간단하고 가볍고 얇게 작성한다.
**도메인 로직을 절대 구현하면 안된다.**

트랜잭션 처리

접근데어?, 이벤트 처리?

### 도메인 로직 넣지 않기
도메인 로직을, 도메인영영과 응용서비스에 분산 할 경우 발생하는 문제
1. 응집성이 떨어짐 (여러 영역을 분석 해야 함)
2. 코드 중복이 발생할 가능성이 높음
결과적으로 변경에 취약함

# 응용 서비스의 구현

### 응용 서비스의 크기

1. 한 응용 서비스 클래스에 도메인의 모든 기능 구현
  - 동일 로직이 있을 경우 중복을 제거하는 장점
  - 관심사가 다른 코드가 섞여 있을 가능성이 높다. SRP 위반 가능성
  - 습관적으로 점점 더 많은 기능이 들어가면 코드 품질은 낮아 짐
2. 구분되는 기능별로 응용 서비스 클래스를 따로 구현
  - 클래스 갯수가 많아지는 단점
  - 각 클래스별로 필요한 객체만 의존하므로 다른 기능을 구현한 코드에 영향을 받지 않음
  - 동일 로직 역시 별로 클래스로 구현하여 중복 제거

### 응용 서비스의 인터페이스와 클래스

인터페이스는 구현이 다수 존재한다거나, 런타임시 구현 객체를 교체해야 할 경우에 필요하다.

### 메서드 파라미터와 값 리턴

도메인을 이용해서 사용자가 요구한 기능을 실행하는데 필요한 값을 파라미터를 통해 전달 받아야 한다.
요청 파라미터가 두개 이상이라면 데이터 절달을 위한 별도 클래스를 사용하는 것이 편리.

응용 서비스에서 Aggregate 자체를 리턴하면 코딩은 편할 수 있지만,
도메인 로직 실행을 응용 서비스와 표현 영역 두 곳에서 할 수 있게 된다.
이는 기능 실행 로직을 응용 서비스와 표현 영역에 분산시켜 코드의 응집도를 낮추는 원인이 된다.
**응용 서비스는 표현 영역에서 필요한 데이터만 리턴하는 것이 기능 실행 로직의 응집도를 높이는 확실한 방법.
표현 영역에서 응용 서비스의 리턴 값을 도메인 객체로 받았다면, 그 결과를 사용자에게 응답할 수 있는 형식으로 변환**

### 표현 영역에 의존하지 않기

응용 서비스의 파라미터 타입을 결정할 때 주의할 점
표현 영역과 관련된 타입을 사용하면 안 된다.(특정 기술 API를 사용하면 안됨)

### 트랜잭션 처리

### 도메인 이벤트 처리
도메인 이벤트란, 도메인에서 발생한 상태변경을 의미.

도메인 영역은 상태가 변경되면 이를 외부에 알리기 위해 이벤트를 발생 시킴

도메인에서 이벤트를 발생시키면 그 이벤트를 받아서 처리하는 곳은 응용 서비스 이다.

이벤트 사용의 장점 ?
- 도메인간의 의존성. 시스템에 대한 의존을 낮춘다.
- 시스템 확장 용이

# 표현 영역
**사용자의 요청 데이터를 응용 서비스가 요구하는 형식으로 변환하고, 응용 서비스의 결과를 사용자에게 응답할 수 있는 형식으로 변환한다.**

# 값 검증
1. 표현 영역
  - 필수 값
  - 값의 형식
  - 범위
2. 응용 서비스
  - 논리 오류 검사(데이터 존재 유무)

# 권한 검사

# 조회 전용 기능과 응용 서비스
조회 전용 모델과 DAO
응용 서비스가 사용자 요청 기능을 실행하는데 별다른 기여를 하지 못한다면,
굳이 서비스를 만들지 않아도 된다.
표현영역에서 조회 전용 기능을 직접 사용해서 처리 하도록 함.
