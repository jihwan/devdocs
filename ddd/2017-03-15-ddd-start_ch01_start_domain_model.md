---
layout: post
title: "ddd-start ch01 도메인 모델 시작"
description: "ddd-start ch02 도메인 모델 시작"
category: DDD
tags: [DDD]
---
{% include JB/setup %}

# 도메인 모델 시작

## 도메인 모델

도메인 모델은 **도메인 자체를 이해하는데 필요한 개념 모델**

**객체를 이용한 도메인 모델**
도메인을 이해하려면 `도메인이 제공하는 기능`과 `도메인의 주요 데이터 구성`을 파악해야 하는데,
이런 면에서 기능과 데이터를 함께 보여주는 객체모델은 도메인을 모델링하기에 적합하다.

도메인 모델은 기본적으로 도메인 자체를 이해하기 위한 개념 모델이다.
개념 모델을 이용해서 바로 코드를 작성할 수 있는 것은 아니기에 구현 기술에 맞는 구현 모델이 따로 필요하다.

> **하위 도메인과 모델** 도메인은 다수의 하위 도메인으로 구성된다. 각 하위 도메인이 다루는 영역은 서로 다르기 때문에 같은 용어라도 하위 도메인 마다 의미가 달라질 수 있다. 도메인에 따라 용어의 의미가 결정되므로, 여러 하위 도메인을 하나의 다이어그램에 모델링하면 안 된다. 모델의 각 구성요소는 특정 도메인을 한정할 때 비로소 의미가 완전해지기 때문에, 각 하위 도메인마다 별도로 모델을 만들어야 한다.

## 도메인 모델 패턴

도메인 모델 패턴은 **아키텍쳐상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴**

도메인 계층은 도메인의 핵심 규칙을 구현한다. 이런 도메인 규칙을 객체지향 기법으로 구현하는 패턴이 도메인 모델 패턴이다.

핵심 규칙을 구현한 코드는 도메인 모델에만 위치하기 때문에 규칙이 바뀌거나 규칙을 확장해야 할 때 다른 코드에 영향을 덜 주고 변경 내역을 모델에 반영할 수 있게 된다.

## 도메인 모델 도출

도메인을 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소, 규칙, 기능을 찾는 것이다.
이 과정은 요구사항에서 출발한다.

## 엔티티와 밸류

무의미한 setter getter 메소드를 만들지 마라.
객체 생성시 필요한 모든 인자를 넘겨라

### 엔티티

- 식별자를 갖는다.
- 식별자는 equals, hashCode overriding 대상

### 밸류 타입

- 개념적으로 완전한 하나를 표현할 때 사용
- 밸류 타입을 위한 기능 추가
- 불변 객체

## 도메인 용어