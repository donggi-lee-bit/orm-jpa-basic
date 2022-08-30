# 엔티티 매핑

## @Entity

- `@Entity`가 붙은 클래스는 `JPA`가 관리한다
- JPA를 사용해 테이블과 매핑할 클래스는 `@Entity`를 붙인다
- **기본 생성자를 필수적으로 명시해줘야함**
- final 클래스, enum, interface, inner 클래스에서는 사용할 수 없다
- 저장할 필드에 `final` 사용 X

## @Table

- 엔티티와 매핑할 테이블 지정
- 매핑할 때 테이블 명을 바꾸고 싶을 때 사용

# 데이터베이스 스키마 자동 생성

- DDL을 애플리케이션 실행 시점에 자동 생성
- 테이블 중심 -> 객체 중심
    - 테이블을 만들어놓고 개발할 필요 없이
    객체를 만들고 매핑을 해놓으면 JPA가 알맞은 테이블을 생성해준다.
    - `@Entity`가 붙은 클래스에 필드를 추가하면 `JPA`가 새로 추가된 필드를 DB 테이블을 만들 때 새로 추가해준다.
- 데이터베이스 방언을 이용해 데이터베이스에 맞는 적절한 DDL 생성
- DDL은 개발할 때만 사용 (운영에선 X)
- 운영에서 사용하려면 DDL을 다듬은 후 사용

## 자동 생성 속성

`hibernate.hbm2ddl.auto`
옵션
- create : 기존 테이블 삭제 후 다시 생성 (drop + create)
- create-drop : 테이블 생성 후 종료 시점에 테이블 drop
- update : 변경분만 반영 (운영 DB에서 사용 X)
- validate : 엔티티와 테이블의 매핑만 확인
- none : 사용하지 않음

### 데이터베이스 스키마 자동 생성 시 주의할 점

- 운영에서는 절대 `create` `create-drop` `update` 를 사용해선 안 된다
- 개발 초기 단계에는 `create` 혹은 `update`
- 테스트 서버는 `update` `validate`
- 스테이징과 운영 서버는 `validate` 또는 `none`

**local에서만 자유롭게 하고 여러 명이 쓰는 개발 서버나 스테이징에서는 가급적 스키마 자동 생성을 사용하지 않는다**

# 필드와 컬럼 매핑

## @Enumerated, @Temporal, @Lob, @Transient

- @Enumerated : 데이터베이스에는 `enum` 타입이 없는데 그 때 @Enumerated 어노테이션을 사용해서 enum처럼 사용한다
    - 주의사항 : `ORDINAL`은 enum 순서를 DB에 저장, `STRING`은 enum 이름을 DB에 저장한다 (기본 값은 `ORDINAL`)
    - enum에 값이 추가되었을 때 `ORDINAL`은 enum 순서대로 DB에 저장하기 때문에 기존에 저장하던 순서와 달리 저장하게 되면 다른 enum임에도 순서로 인해 같은 숫자가 저장될 수 있다.
- @Temporal : 날짜 데이터를 DB에 넣을 때 사용한다
- @Lob : `varchar`를 넘어서는 큰 컨텐츠를 넣고 싶을 때 사용한다
    - 매핑하는 필드 타입이 문자면 `CLOB`으로 매핑, 나머지는 `BLOB`으로 매핑된다
- @Transient : DB에 넣고싶지 않아 객체 매핑을 하고 싶지 않을 때 사용한다

## 기본 키 매핑

- 직접 할당 : `@Id` 사용
- 자동 생성 : `@GeneratedValue`
    - `AUTO` : 데이터베이스 방언에 따라 자동 지정, strategy 설정 안할 시 기본값
    - `IDENTITY` : 데이터베이스에 위임, MySQL 사용 시
    - `SEQUENCE` : 데이터베이스 시퀀스 오브젝트 사용, ORACLE DB 사용 시
        - `@SequenceGenerator`를 사용하여 시퀀스 설정해줄 수 있음
    - `TABLE` : 키 생성용 테이블 사용, 모든 DB에서 사용
        - 데이터베이스 시퀀스를 흉내낸다
        - 최적화가 되어있지 않기 때문에 성능이 떨어진다는 단점이 있다.
        - `@TableGenerator`