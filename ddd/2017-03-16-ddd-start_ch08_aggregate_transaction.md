---
layout: post
title: "ddd-start ch08 Aggregate Transaction 관리"
description: "ddd-start ch08 Aggregate Transaction 관리"
category: DDD
tags: [DDD]
---
{% include JB/setup %}

# Aggregate 와 Transaction

# 선점 잠금 (Pessimistic Lock)
먼저 Aggregate를 구한 thread가 Aggregate 사용이 끝날 때까지 다른 thread가 해당 Aggregate를 수정하는 것을 막는 방식

JPA에서 LockModeType.PESSIMISTIC_WRITE 이용.

### 선점 잠금과 교착 상태

잠금을 구할 때 최대 대기 시간을 지정한다.

```java
Map<String, Object> properties = new HashMap<>();
properties.put("javax.persistence.lock.timeout", 1000);
em.find(?, ?, LockModeType.PESSIMISTIC_WRITE, properties);
```

> DBMS에 따라 적용이 안될 수도 있다.
DBMS에 따라 교착 상태에 빠진 커넥션을 처리하는 방식이 다르다.
쿼리별로 대기시간 지정 or 커넥션단위로 대기시간 지정.

# 비선점 잠금 (Optimistic Lock)
변경할 데이터를 실제 DBMS에 반영하는 시점에 변경 가능 여부를 확인하는 방식

Aggregate에 버젼으로 사용할 숫자 타입 프로퍼티를 추가해야 함.
Aggregate를 수정할 때마다 버젼 프로퍼티 값은 1씩 증가.

단점으로는 화면쪽과 연동을 할 경우 주고 받는 데이터에 버젼 정보가 있어야 한다.

주고 받은 버젼의 체크는 응용 서비스에서 수행.

익셉션 처리는 표현 영역에서 수행 (OptimisticLockingFailureException | VersionConflictException)

### 강제 버젼 증가

```java
em.find(?, ?, LockModeType.PESSIMISTIC_FORCE_INCREMENT);
```

# 오프라인 선점 잠금
