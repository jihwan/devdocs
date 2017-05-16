---
layout: post
title: "ddd-start ch04 레포지터리와 모델구현"
description: "ddd-start ch04 레포지터리와 모델구현"
category: DDD
tags: [DDD]
---
{% include JB/setup %}

# JPA를 이용한 Repository 구현

### 모듈 위치
repository는 Aggregate Root 기준 interface로 구현하며, domain package에 위치
구현체는 infra package에 위치

### Repository 기본기능 구현

- [X] findById
- [X] save
- [] findByXXX
- [] delete (진짜 삭제하지 않고 삭제 flag를 두는 방법도 있음)

# 매핑구현

### Entity와 Value 기본 매핑 구현

```java
@Entity
@Embeddable
@Embedded
@AttributeOverrides
@AttributeOverride
```

### 기본생성자
@Entity, @Embeddable protected 기본 생성자 작성.
ValueObject는 불변 객체로 작성

### 필드 접근방식 사용

### AttributeConverter를 이용한 Value 매핑 처리

### Value Collection: 별도 테이블 매핑

```java
@ElementCollection
@ElementTable // table 이름, joinColums 지정
@OrderColumn
```

여기서 중요한 점은 `@OrderColumn` 지정 여부 이다.
지정하지 안을 경우, Value Collection 쪽에 변경(add, remove)을 가하면,
부모쪽 key를 이용하여 모든 data를 delete 후 insert 를 한다는 점이다.

### Value Collection: 한개 컬럼 매핑
AttributeConverter를 이용하여 comma delimeter로 처리

### Value를 이용한 ID 매핑

### 별도 테이블에 저장하는 Value 매핑
Aggregate 에서 Root Entity를 뺀 나머지 구성요소는 대부분 Value 이다.
Root Entity 외에 또 다른 Entity가 있다면, 진짜 Entity 인지 의심해봐야 한다.
단지 별도 Table에 데이터를 저장한다고 해서 Entity 인것은 아니다.

Value가 아니라 Entity가 확실하다면, 다른 Aggregate는 아닌지 확인해야 한다.
특히, 자신만의 독자적인 life-Cycle 을 갖는다면 다른 Aggregate일 가능성이 높다.

Aggregate에 속한 객체가 Value인지 Entity인지 구분하는 방법은
고유식별자를 갖는지 여부를 확인해야 한다. 하지만, 식별자를 찾을때 매핑되는
테이블의 식별자를 Aggregate 구성요소의 식별자와 동일한 것으로 착각하면 안된다.
별도 테이블로 저장되고 테이블에 PK가 있다고 해서 테이블과 매핑되는 Aggregate 구성요소가
고유 식별자를 갖는것은 아니다.

Article 과 ArticleContent 예에서 `@SecondaryTable` 을 이용하였다.
이때는 성능을 고려해서 조회전용쿼리를 만든다. 이 내용은 5장에서 언급

### Value Collection을 `@Entity`로 매핑하기
책에서는 1:N 단방향 연관관계를 이용하였는데, 이는 update 쿼리가 발생하므로 않좋은것 같다.
1:N, N:1 양방향 관계를 만들고 N 쪽에서 정의한 객체 참조 관계를 사용하지 못하게 하는 방법이 더 좋은거 같다.

책에서는 성능을 고려하여 다형성을 포기하고 Entity에서 다시 Value로 매핑하는 방법 또한 제시했다.

### ID 참조와 조인 테이블을 이용한 단방향 M:N 매핑

> 이런 내용이 가능한 것은, 도메인 규칙과 요구사항에 기인한 것이다.
일대일, 일대다, 다대일, 다대다 관계에서 DB세계와 객체 세계는 다르다.
양방향관계가 아닌 단방향 관계일 수 있다!

# 애그리거트 로딩 전략
Aggregate에 속한 객체가 모두 모여야 완전한 하나가 된다. 즉, Aggregate 루트를 로딩하면
루트에 속한 모든 객체가 완전한 상태여야 함을 의미한다.

Collection 매핑에서 EAGER fetch를 할 경우, 카테시안곱 문제가 발생한다.
Aggregate는 개념적으로 하나여야 한다. 그러나, 경우에 따라 루트 Entity를 로딩하는 시점에
Aggregate에 속한 객체를 모두 로딩해야 하는 것은 아니다.

Aggregate가 완전해야 하는 이유
- 상태 변경시 Aggregate 상태가 완전해야 함
- 표현영역에서 Aggregate 상태정보를 보여줄 경우 (별도 조회 전용 기능을 통해서 이것은 극복 가능)

결과로, 상태 변경을 위해 Aggregate 의 완전한 로딩이 필요하겠지만 꼭 그래야 할 필요는 없다.
JPA는 트랜잭션 구간에서 지연로딩을 허용하기 때문이다.

**결론, 상황에 맞추어 즉시,지연 로딩을 결정하라.
불확실 하다면, 지연 로딩으로 한다.
모든 데이터가 완벽히 로딩되어야 한다면 Query 이용**

# Aggregate의 영속성 전파
- 저장 : Aggregate 루트와 Aggregate에 속한 모든 객체를 저장
- 삭제 : Aggregate 루트와 Aggregate에 속한 모든 객체를 삭제

CASCADE, orphanRemovable 이용

# 식별자 생성 기능
- 사용자 직접 생성
- 도메인 로직으로 생성 (도메인 서비스로 구현)
- DB를 이용한 일련번호 사용 (Repository에 구현, DB의 자동증가 컬럼은 불필요)
