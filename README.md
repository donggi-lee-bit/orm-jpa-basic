# orm-jpa-basic
자바 orm 표준 jpa 프로그래밍 강의 레포

# SQL 중심적인 개발의 문제점

데이터베이스 SQL만 알아들을 수 있다.
그러다보니 SQL 중심적인 개발이 되는 것이 문제.

**무한 반복과 지루한 코드**
- 기능을 추가해서 테이블을 하나 만들면
  CRUD 쿼리를 다 짜야한다.
- 그 때마다 Java 객체를 SQL로 바꾸고, SQL을 Java 객체로 바꾸어주어야한다.
- 계속 비슷한 일만 하고 있다는 생각이 들게 됨

예를 들어
- 요구사항이 변경되어서 필드가 추가되면 그에 맞추어 쿼리도 다 수정해야한다.

**패러다임의 불일치**
- 관계형 데이터베이스가 나온 사상과
  객체지향의 사상은 다르다.
- 관계형 데이터베이스는 데이터를 잘 정규화하여 보관하는 게 목표이고,
  객체지향은 속성과 기능, 필드와 메서드를 잘 캡슐화하여 사용하는 게 목표다.

**객체와 관계형 데이터베이스의 차이**
- 객체에는 상속관계가 있으나, 관계형 데이터베이스에는 상속관계가 존재하지 않는다.
  - 상속 관계의 객체를 SQL로 바꾸기 위해서는 상속 클래스, 하위 클래스 두 번의 쿼리를 만들어서 날려줘야한다.
  - DB에서 조회할 때에도 join 쿼리를 만드는 등 조회하는데 복잡함
- 객체는 참조를 가지고 있고, 데이터베이스는 Pk와 fk로 조인을 해서 필요한 데이터를 찾을 수 있다.


**연관관계**
- 객체는 참조를 사용함
  - 객체는 한 방향으로만 참조할 수 있다
- 테이블은 외래 키를 사용한다
  - 테이블은 PK와 FK로 반대로도 참조할 수 있다

**객체지향적으로 설계했을 때 데이터베이스에서 데이터에 접근할 때의 문제점**
- 객체 내부에 객체 필드를 갖고 있어 객체를 통해 내부에 필드로 존재하는 객체에 접근하려고 하면 Join 문을 통해서 여러 과정을 거쳐 접근할 수 있다.
- 객체지향적 설계를 통해 객체가 늘어나게 되면 해당 객체의 데이터를 접근하는 작업은 더 번잡해지게 된다.

이런 객체를 컬렉션으로 관리할 수 있다면 바람직한 설계가 되겠지만 이 상황에서는 객체를 컬렉션으로 관리 할 수 없다.

# JPA

- JPA란 Java Persistance API
- Java 진영의 ORM 기술 표준이다

### ORM이란

- ORM이란 Object-Relational Mapping (객체 관계 매핑) 이다. 
- 객체는 객체대로 설계하고
  관계형 데이터베이스는 관계형 데이터베이스대로 설계하여 ORM 프레임워크가 중간에서 매핑해준다.
- 대중적인 언어에는 ORM이 존재한다.

### JPA는 애플리케이션과 JDBC 사이에서 동작한다
- JPA는 애플리케이션이 명령 하면 JDBC API를 사용하여 SQL문을 만들어 DB에 접근해 요청한 데이터를 받아올 수 있도록 한다.

### JPA 동작
- JPA에게 객체 id를 전달하면 JPA는 매핑 정보를 바탕으로 SQL문을 생성하여 Jdbc API를 사용하여 객체를 가져올 수 있다.
- 이 동작을 통해 객체지향 프로그래밍과 관계형 데이터베이스의 패러다임 불일치를 해결해준다.

### JPA를 사용하면
- SQL 중심적인 개발에서 객체 중심으로 개발이 가능해진다.
- 생산성 향상
- 유지보수에 용이
  - 객체 필드의 순서를 바꾸어도 쿼리를 그에 맞게 수정해주지 않아도 된다. JPA가 SQL을 처리해주기 때문.
- 패러다임 불일치 해결
- 성능 향상
- 데이터 접근 추상화와 벤더 독립성
- 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장한다.

### JPA의 성능 최적화 기능

JPA를 사용함으로서 다음과 같은 성능 최적화가 된다.
- 1차 캐시와 동일성(Identity) 보장 
  - 같은 트랜잭션 안에서는 같은 엔티티 반환
  - DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read 보장
- 트랜잭션을 지원하는 쓰기 지연 (Transactional Write-Behind)
  - 트랜잭션을 커밋할 때까지 INSERT SQL을 모은다.
  - JDBC BATCH SQL 기능을 사용해서 한 번에 SQL을 전송한다.
- 지연 로딩 (Lazy Loading)
  - 지연 로딩은 객체가 실제 사용될 때 로딩
  - 즉시 로딩은 JOIN SQL로 한 번에 연관된 객체끼리 미리 조회한다

# hibernate

- JPA는 Interface이다
- hibernate는 JPA의 구현체로 사용되는 것

# 데이터베이스 방언

- JPA는 특정 데이터베이스에 종속되지 않는다
- 각각의 데이터베이스가 제공하는 SQL 문법과 함수는 조금씩 다르다
  - 가변 문자 : MySQL은 VARCHAR, Oracle은 VARCHAR2
  - 페이징 : MySQL은 LIMIT, Oracle은 ROWNUM
- 방언 : SQL 표준을 따르지 않는 특정 데이터베이스만의 고유한 기능

<img width="534" alt="Screen Shot 2022-08-24 at 5 53 26 PM" src="https://user-images.githubusercontent.com/73376468/186376503-e9b1c7a5-d426-4b4e-93c1-fc08296f727d.png">

- 강의는 h2 데이터베이스를 사용하므로 hibernate.dialect의 value 값으로 `org.hiberante.dialect.H2Dialect` 를 사용하겠다고 명시
- 속성의 `javax` 와 `hibernate` 차이
  - javax는 hibernate 라이브러리가 아닌 다른 JPA 구현 라이브러리를 사용해도 그대로 적용이 됨
  - `hibernate`가 붙은 것은 hibernate 전용 옵션이라고 보면 됨

# 매핑 정보를 통한 JPA의 동작

- `EntityManagerFactory`는 애플리케이션 로딩 시점에 하나만 만들어야한다.
- 일괄적인 트랜잭션 단위의 작업을 할 때 `EntityManager`를 꼭 만들어야 한다.
- JPA에서는 데이터를 변경하는 작업을 트랜잭션안에서 해야한다.

### EntityManager의 transaction

- JPA에서 데이터를 저장, 변경하는 작업을 트랜잭션 안에서 하기 위해 트랜잭션을 가져온다
  - `EntityTransaction tx = em.getTransaction();`
- 트랜잭션을 가져온다고 범위 내에 있는 게 아니기 때문에 `begin()` 메서드로 트랜잭션의 시작을 알린다.
- 작업을 수행하고 `persist()` 메서드로 해당 작업을 영속성 컨텍스트에 저장한다.
- `commit()`으로 커밋하거나, 해당 작업을 되돌리겠다면 `rollback()` 메서드를 호출한다.
- `EntityManagerFactory`는 하나만 생성하여 애플리케이션 전체에서 공유한다
- 그에 반해 `EntityManager`는 하나의 작업 단위가 끝나면 종료하여야 한다
  - `EntityManager`를 스레드 간 공유하면 안 된다
- **JPA의 모든 데이터 변경은 트랜잭션 안에서 실행해야 한다**

### JPA를 통해 값을 가져와서 데이터를 변경하면

- JPA를 통해 `Entity`를 가져오면, JPA가 해당 `Entity`를 관리한다
- 데이터의 변경이 있었는지 `commit`하는 시점에서 JPA가 확인한다
  - 변경이 있으면 `commit` 하기 직전 `update query`를 날린다

# JPQL

## JPQL이란

- `Java Persistence Query Language`를 말한다.
- `JPA` 가 `SQL`을 추상화한 `JQPL`이라는 객체 지향 쿼리 언어 제공
- 관계형 데이터베이스에 사용될 `query`를 만든다
- `SQL` 문법 베이스로 개발되었다
- `SELECT` 절을 사용하여 정보 또는 데이터를 검색하고, `UPDATE` 절과 `DELETE` 절을 사용하여 대량 업데이트를 수행할 수 있다
- `EntityManager.createQuery()`를 사용하여 동작한다
- `JPQL`은 `entity 객체`를 대상으로 쿼리
- `SQL`은 `데이터베이스 테이블`을 대상으로 쿼리

**JPQL로 짜여진 query는 JPA 입장에서 table이 아닌 객체를 대상으로 query를 날린다**

## JPQL의 장점

### Paging 처리가 가능하다

- `setFirstResult()` `setMaxResults()` 메서드를 사용하여 `paging` 처리를 할 수 있다