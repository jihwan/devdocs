---
layout: post
title: "ddd-start ch05 Repository의 조회기능"
description: "ddd-start ch05 Repository의 조회기능"
category: DDD
tags: [DDD]
---
{% include JB/setup %}

# 검색을 위한 스펙
검색 조건이 다양해 짐에 따라 여러 종류의 find method를 작성할 수 있겠으나,
이는 비효율적이고, 중복코드의 양산만 야기할 뿐이다.
**검색 조건이 다양할 경우 스펙을 이용하여 해결하도록 하자**

# 스펙 조합

# JPA를 위한 스펙 구현
### JPA 스펙 구현
### AND/OR 스펙 조합을 위한 구현
### 스펙을 사용하는 JPA Repository 구현

# 정렬 구현

# 페이징과 갯수 구하기 구현

# 조회 전용 기능 구현
Repository는 Aggregate의 저장소를 표현하는 것으로서 다음 용도로 Repository 사용은 부적합하다.
- 여러 Aggregate를 조합해서 한 화면에 보여주는 데이터 제공
- 각종 통계 데이터 제공

**하이버네이트 이용시, 동적인스턴스, @Subselect, nativeSQL 로 해결.
ORM 기술을 사용한다고 해서 모든 처리를 ORM 기술로 한다는 것은 오남용이고 비효율적이다.**

### 동적 인스턴스 생성
조회전용모델을 작성하자

### 하이버네이트 @Subselect

```java
@Entity
@org.hibernate.annotations.Immutable
@org.hibernate.annotations.Subselect
@org.hibernate.annotations.Synchronize
```

# 참고

[Advanced Spring Data JPA - Specifications and Querydsl](https://spring.io/blog/2011/04/26/advanced-spring-data-jpa-specifications-and-querydsl/)
