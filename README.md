# DDD 스터디
- [최범균님 책](https://product.kyobobook.co.kr/detail/S000001810495) 으로 스터디한 내용 정리
- 우테캠 Pro 5기에서 스터디 모집

## 1장. 도메인 모델 시작하기
- SW로 해결하고자 하는 문제 영역을 도메인이라고 한다. (ex. 온라인 서점)
- 도메인 모델은 특정 도메인을 개념적으로 표현한 것. 도메인 자체를 이해하기 위한 개념 모델이다.
- 도메인 모델의 구성은 크게 엔티티와 밸류로 구분할 수 있다. 

### 엔티티 & 밸류
- 엔티티의 가장 큰 특징은 식별자를 가진다는 것이다.
- 밸류 타입을 사용하여 코드의 의미를 더 잘 이해할 수 있다.
- 밸류 타입이 데이터를 변경할 때는, 기존 데이터를 변경하는게 아니라 새로운 밸류 객체를 생성한다.
- 도메인 모델에 setter 메소드는 최대한 사용하지 않는다.
  - setter 메소드를 통해 객체를 생성하면, 실수로 온전하지 않은 객체를 생성할 여지가 있다.
  - 도메인 객체가 불완전한 상태로 생성되는 것을 막으려면 생성자를 통해 필요한 데이터를 모두 받아야 한다.

## 2장. 아키텍처 개요

### 네 개의 영역
- 표현, 응용, 도메인, 인프라스트럭처는 아키텍처를 설계할 때 출현하는 전형적인 네 가지 영역이다.
- 응용 영역은 기능을 구현하기 위해 도메인 영역의 도메인 모델을 사용한다. 즉, 로직을 직접 수행하기 보다는 도메인 모델에 로직 수행을 위임한다.
- 도메인 영역인 도메인 모델은 도메인의 핵심 로직을 구현한다.
- 인프라스트럭처 영역은 구현 기술에 대한 것을 다룬다. RDBMS 연동이나 메시징 큐, REDIS나 SMTP를 이용한 메일 발송 기능 구현도 포함된다.
- 도메인, 응용, 표현 영역은 구현 기술을 직접 만들지 않고, 인프라스트럭처 영역에서 제공하는 기능을 사용해 필요한 기능을 개발한다.

### 계층 구조 아키텍처
- 위의 네 영역을 구성할 때 표현 영역 -> 응용 -> 도메인 -> 인프라 영역을 사용한다.
- 계층 구조는 특성 상, 상위 계층에서 하위 계층으로 의존만 존재하고 하위 계층은 상위 계층에 의존하지 않는다. 예를 들어 표현 계층은 응용 계층에
의존하지만 도메인 계층이 응용 계층에 의존하지는 않는다.
- 고수준 모듈(응용, 도메인)이 저수준 모듈(인프라)에 의존하면 테스트 어려움과 기능 확장의 어려움이라는 두 가지 문제가 발생한다.
DIP를 통해 이를 해결할 수 있다.

### DIP
- 추상화 된 인터페이스를 사용해 Service가 인터페이스를 사용하도록 하면 의존성을 줄일 수 있다. 
- 고수준 모듈이 저수준 모듈을 사용하려면 고수준 모듈이 저수준 모듈에 의존해야 한다. 반대로 저수준 모듈이 고수준 모듈에 의존한다고 해서
이를 DIP(Dependency Inversion Principle) 의존 역전 원칙이라고 부른다.
- DIP를 사용하게 되면 고수준 모듈은 더 이상 저수준 모듈에 의존하지 않고 구현을 추상화한 인터페이스에 의존한다. 실제 사용할 저수준 구현
객체는 외부에서 의존성을 주입하는 방식으로 사용한다.
- DIP를 오해하여 구현 클래스와 인터페이스를 분리하는 정도로 생각하면 안 된다. 가령 RuleEngine을 인터페이스로 추출했다고 가정하자.
Service 입장에서 볼 땐 룰엔진은 중요하지 않고 규칙에 따라 금액을 계산한다는 것이 중요하다. 

### 도메인 영역의 주요 구성요소
- 도메인 영역을 구성하는 요소로는 엔티티, 밸류, 애그리거트,리포지터리, 도메인 서비스가 있다.
- 엔티티는 데이터와 함께 기능을 제공하는 객체이다. 또 엔티티는 두 개 이상의 데이터가 개념적으로 하나인 경우 밸류 타입을 이용해서 표현할 수 있다.
- 도메인이 커질수록 많은 엔티티와 밸류가 출현하는데 애그리거트를 통해 개념적으로 묶을 수 있다. 애그리거트를 사용하면 개별 객체가 아닌 관련 객체를
묶어 군집 단위로 바라볼 수 있으며 큰 틀에서 도메인 모델을 관리할 수 있다.
- 애그리거트는 군집에 속한 객체를 관리하는 루트 엔티티를 갖는다. 애그리거트 루트를 통해 내부의 엔티티와 밸류 객체에 접근한다. 이렇게
내부 구현을 숨겨서 구현을 캡슐화 할 수 있다.
- 도메인 모델 관점에서 우리가 쓰는 Repository는 객체를 영속화하는데 필요한 기능을 추상화 한 것으로 고수준 모듈에 속하고 이를 구현한
JPARepository는 저수준 모듈에 속한다.

### 인프라스트럭처 개요
- 무조건 인프라스트럭처에 대한 의존성을 없앨 필요는 없다. 스프링의 @Transactional 이나 JPA의 @Enitity를 사용하는 것이
xml 설정을 이용하는 것보다 편리하다. 
- 구현의 편리함은 DIP의 장점(변경 유연함, 테스트 쉬움) 만큼 중요하기 때문에 장점을 해치지 않는 범위에서 구현 기술에 대한 의존을 가져가는 것이
나쁘지는 않다고 생각한다. 오히려 의존을 완전히 없애려고 시도하면 구현을 더 복잡하게 만들 수 있다.

## 3장. 애그리거트

### 3.1 애그리거트
- [캡쳐 두개 보여주기.]
- 수 백개의 도메인 모델이 있는데 개별 단위로 모델을 보게 되면 전반적인 구조나 큰 수준에서 도메인 간의 관계를 파악하기 어려워진다.
- 상위 수준에서 모델이 어떻게 엮여 있는지 알아야 요구사항을 쉽게 반영할 수 있다.
- 복잡한 도메인을 관리하기 쉬운 상위 모델에서 볼 수 있는 방법이 필요한데 그 방법이 애그리거트다.
- 수 많은 객체를 애그리거트로 묶어서 바라보면 도메인 모델 간의 관계를 비교적 쉽게 파악할 수 있다.
- 한 애그리거트에 속한 객체는 유사하거나 동일한 라이프 사이클을 갖는다. 즉, Order, OrderLine, Orderer 등의 객체는 대부분 함께 생성되고 제거된다.
- 애그리거트의 경계르 설정할 때 기본이 되는 것은 도메인 규칙과 요구사항이다. 도메인 규칙에 따라 함께 생성되는 구성요소는 한 애그리거트에 속할 가능성이 높다.
- 함께 생성되고 함께 변경된다면 같은 애그리거트에 속할 가능성이 높다.
- Product와 Review
  - Product 하나를 보면 Review를 볼 수있다.
  - 하지만. Product와 Review가 동시에 생성되진 않는다.
  - Product가 바뀐다고 Review가 바뀌거나 Review가 바뀐다고 Product가 바뀌진 않는다.
  - 따라서 이 둘은 다른 애그리거트에 속한다.
- 저자의 경험에 따르면 다수의 애그리거트가 한 객의 엔티티 객체만 갖는 경우가 많았으며 두 개 이상의 엔티티로 구성되는 애그리거트는 드물었다.

### 3.2 애그리거트 루트
- 애그리거트의 모든 객체가 일관된 상태를 유지하려면 전체를 관리할 주체가 필요한데 이 책임을 지는게 애그리거트 루트 엔티티다.
- 애그리거트 루트의 핵심은 애그리거트의 일관성이 꺠지지 않도록 하는 것이다. 따라서 주문 애그리거트는 배송지 변경, 상품 변경과 같은 기능을 제공하고
애그리거트 루트가 이 기능을 구현한 메서드를 제공한다.
- 애그리거트 외부에서 애그리거트에 속한 객체를 직접 변경하면 안된다. 즉 애그리거트 루트는 연관된 객체에게 메시지를 던져야지 직접 값을 꺼내와
변경하면 안 된다. 
- 애그리거트 루트를 통해서만 도메인 로직을 구현하려면 두 가지 습관을 적용하자
  - set 메서드를 public 하게 만들지 않는다.
  - 밸류 타입은 불변으로 구현한다.
- public set 메소드는 의도를 표현하지 못하고 도메인 내부가 아닌 응용이나 표현 계층으로 분산시킨다.
- 애그리거트 루트가 도메인 규칙을 올바르게 구현하면 애그리거트 전체의 일관성을 오바르게 유지 할 수 있다.
- 애그리거트 외부에서 애그리거트의 객체를 변경할 수 없도록 불변 객체로 만들거나 메소드를 패키지나 protected 범위로 한정하여
외부에서 실행될 수 없도록 한다.
- 트랜잭션 범위는 작을수록 좋다. 한 트랜잭션에서는 한 개의 애그리거트만 수정해야 한다. 이는 애그리거트에서 다른 애그리거트를 변경하지 않는다.
- 부득이하게 한 트랜잭션으로 두 개 이상의 애그리거트를 수정해야 한다면 애그리거트에서 다른 애그리거트를 직접 수정하지 말고
서비스에서 두 애그리거트를 수정하도록 구현한다.

### 3.3 리포지터리와 애그리거트
- 리포지터리는 애그리거트 단위로 존재한다.
  - DB에 Order와 OrderLine이 따로 저장되더라도 Order와 OrderLine을 위한 리포지터리를 각각 만들지 않는다. 
  - Order가 루트이므로 Order를 위한 리포지터리만 존재한다.
  - 예를 들어 Order와 관련된 테이블이 세 개라면 Order 애그리거트를 저장할 때 매핑된 테이블에 데이터를 저장해야 한다.
  - 동일하게 애그리거트를 구할 때 OrderLine, Orderer 등의 모든 구성요소를 포함해야 한다.

### 3.4 ID를 이용한 애그리거트 참조
- 애그리거트에서 다른 애그리거트를 참조한다는 것은 다른 애그리거트의 루트를 참조한다는 것과 같다.
- 애그리거트 간의 참조는 필드를 통해 쉽게 구할 수 있다. 하지만 필드를 이용한 애그리거트 참조는 다음의 문제를 야기한다.
  - 편한 탐색 오용 (한 애그리거트 내부에서 다른 애그리거트 객체에 쉽게 접근할 수 있으면 다른 애그리거트의 상태를 쉽게 변경할 수 있다.)
  - 성능 문제 (상황에 따라 즉시로딩을 할지 지연로딩을 할지 전략을 결정해야 함)
  - 확장 (서비스가 커지면서 JPA와 같은 단일 기술을 사용할 수 없다면 필드를 통해 참조가 안될 수도 있다.)
- 이 문제를 완화하기 위해 ID를 이용해서 다른 애그리거트를 참조하는 방법을 사용할 수 있다.
  - ID 참조를 사용하면 한 애그리거트에 속한 객체들만 참조로 연결된다. 이는 애그리거트의 경계를 명확히 하고 애그리거트 간 물리적인 연결을
    제거해준다.
  - ID를 이용하면 기능에 따라 N+1 문제와 비슷한 코드를 작성해야 하는 일이 있는데 이럴 때는 그 기능을 위한 join 쿼리를 만들어 제공한다.

### 3.5 애그리거트 간 집합 연관
- 개념적으로 1-N, M-N 관계는 실제 요구사항을 충족하는 것과는 상관없을 때가 있다.
- 가령 페이지네이션이나, 상세 정보를 들어가야 보여주는 관계라면 연관 관계로 조회보다 id나 id 집합으로 푸는 것이 성능적으로 좋을 때가 많다.

### 3.6 애그리거트를 팩토리로 사용하기
- 도메인의 상태나 값을 체크하는 로직은 서비스 layer 보다는 도메인 로직에 들어가는게 좋다.
- 애그리거트가 갖고 있는 데이터를 이용해 다른 애그리거트를 생성해야 한다면 팩토리 메소드를 고려해 보자.

## 4장. 리포지터리와 모델 구현

### 4.1 JPA를 이용한 리포지터리 구현
- 리포지터리 인터페이스(JPA에서 우리가 작성하는 리포지터리)는 도메인 영여겡 속하고 실제 구현체(JPA를 쓴다면 JPQL을 쓰는 메소드)는
  infra 영역에 둬야 한다.
- Q.가능하면 리포지터리 구현 클래스를 인프라스트럭처 영역에 위치시켜서 인프라스트럭처에 대한 의존을 낮춰야 한다.

### 4.3 매핑 구현

#### 엔티티와 밸류 기본 매핑 구현
- 애그리거트 루트는 엔티티이므로 @Entity로 매핑 설정한다.
- 밸류는 @Embeddable로 매핑하고 밸류 타입 프로퍼티는 @Embedded로 매핑 설정한다.

#### 필드 접근 방식
- 엔티티 프로퍼티에 get/set 메소드를 추가하면 도메인의 의도가 사라지고 객체가 아닌 데이터 기반으로 엔티티를 구현할 가능성이 높아진다.
- 특히 set 메소드는 내부 데이터를 외부에서 변경할 수 있는 수단이 되기 때문에 캡슐화를 꺠는 원인이 될 수 있다.
- 엔티티는 의도가 잘 드러나는 기능을 제공해야 한다. setState 보다는 cancel 메소드가 도메인을 더 잘표현하고
  setShippingInfo 보다는 changeShippingInfo가 의도를 더 잘 나타낸다.

#### AttributeConverter를 이용한 매핑 처리
- 밸류 타입의 여러 프로퍼티를 DB의 한 개 컬럼에 매핑해야 할 때 AttributeConverter를 사용할 수 있다.

#### 별도 테이블에 저장하는 밸류매핑
- 애그리거트에서 루트 엔티티를 제외한 나머지 구성 요소는 대부분 밸류이다.
- 단지 별도 테이블에 데이터를 저장한다고 엔티티는 아니다. 예를 들어 OrderLine을 별도 테이블에 저장하지만 OrderLine 자체는 엔티티가 아니라 밸류다.
- 애그리거트에 속한 객체가 밸류인지 엔티티인지 구분하는 방법은 고유 식별자를 갖는지 확인하는 것이다. ID 컬럼이란 식별자를 가지고 있다 해서
  엔티티라고 착각하면 안 된다. 이 때의 ID는 애그리거트 루트와 연결하기 위함이다.

#### ID 참조와 조인 테이블을 이용한 단방향 M-N 매핑
- 애그리거트 간 집합 연관은 성능 상의 이유로 피하는게 좋은데, 요구사항을 구현하기 위해 집합 연관을 사용하는게 유리하다면
  ID 참조를 이용한 단방향 집합 연관을 적용할 수 있다.

### 4.4 애그리거트 로딩 전략
- 즉시 로딩으로 애그리거트에 속한 모든 객체를 로딩한다는 것은 항상 좋은 것은 아니다. 특히 컬렉션에 대해 EAGER르 설정하면 오히려 문제가 될 수 있다.
- 조회 성능 때문에 즉시 로딩 방식을 사용하지만 이렇게 조회되는 연관 객체들이 많아진다면 성능(실행 빈도, 지연 로딩시 실행 속도)을 검토해야 한다.
- 애그리거트는 개념적으로 하나여야 하지만 루트 엔티티를 로딩하는 시점에 애그리거트에 속한 모든 객체를 모두 로딩해야 하는 것은 아니다.
  애그리거트가 완전해야 하나는 두 가지 이유는
  - 상태를 변경하는 기능을 실행할 때 에그리거트 상태가 완전해야 하고
  - 애그리거트의 정보를 보여줄 때 필요하다.
- 이 중 두번째 데이터를 보여주는 측면에서는 별도의 조회 전용 기능과 모델을 구현하는 방식을 사용하는게 유리하기에 애그리거트의 완전한 로딩은
  상태 변경과 더 관련이 있다. 상태 변경을 위해 애그리거트를 완전히 로딩할 필요는 없으며 상태를 변경하기 위해 필요한 구성요소만 로딩해도 된다.
- 지연 로딩은 동작방식이 항상 동일한 반면에 즉시 로딩은 Entity, Embeddable, JPA 프로바이더에 따라 달라질 수 있다. 물론 지연 로딩은
  즉시 로딩보다 쿼리 실행 횟수가 많아지기 때문에 애그리거트에 맞게 즉시로딩과 지연로딩을 선택해야 한다.

### 4.5 애그리거트의 영속성 전파
- 애그리거트가 완전한 상태여야 한다는 것은 저장하고 삭제할 때도 하나로 처리되어야 한다.
- @Embeddable 매핑 타입은 함께 저장되고 삭제되므로 cascade 옵션을 설정하지 않아도 된다.
- 반면에 애그리거트에 속한 @Entity 타입에 대한 매핑은 cascade 속성을 사용해서 저장과 삭제시에 동시에 처리되도록 설정한다.

### 4.6 식별자 생성 기능
- 식별자 생성 규칙이 있다면 엔티티를 생성할 때 별도 서비스로 식별자 생성 기능을 분리해야 한다.
- 식별자 생성 규칙은 도메인 규칙이므로 도메인 영역에 도메인 서비스를 두고, Service layer에서 도메인 서비스를 이용해 식별자를 구하고 엔티티를 생성한다.
- 도메인 서비스 말고 또 다른 장소가 있다면 리포지터리이다. 아래와 같이 메서드를 추가한 뒤 구현 클래스에서 알맞게 구현하면 된다.
```java
public interface ProductRepository {
    // 식별자 생성 메소드
    ProductId nextId();
}
```

### 4.7 도메인 구현과 DIP
- 도메인에서 @Entity, @Table, @Id와 같은 에노테이션을 쓴다는 것은 **도메인 모델**이 영속성 구현 기술인 **JPA에 의존**하고 있다는 의미이다.
- 구현 기술에 대한 의존없이 도메인을 순수하게 유지하려면 JPA의 Repository 인터페이스를 상속받지 않도록 수정하고 Repository 인터페이스를 구현한
  클래스를 인프라에 위치시켜야 한다. 또 도메인에서 @Entity, @Table 등 JPA에 특화된 에노테이션을 지워야 한다.
- 하지만 **리포지터리와 도메인 모델의 구현 기술은 거의 바뀌지 않는다**. 글의 저자는 JPA로 구현한 기술을 마이바티스 등의 기술로 변경한 적이 없고
  RDBMS를 사용하다 몽고 DB로 바꾼적도 없다. 이렇게 변경이 거의 없는 상황에서 변경을 미리 대비하는 것은 과하다고 생각하여 글의 저자는
  애그리거트 도메인 모델을 구현할 때 타협했다고한다.
- DIP를 완벽하게 지키면 좋겠지만 개발 편의성과 실용성 측면에서 JPA 에노테이션을 도메인 모델에 사용하는 방법이 합리적이라 생각한다.

## 5장. 스프링 데이터 JPA를 이용한 조회 기능

### 5.1 시작에 앞서
- CQRS란 명령모델과 조회모델을 분리하는 패턴이다.
- 엔티티, 애그리거트에서 살펴본 모델은 주로 상태를 변경할 때 사용된다. 즉 도메인 모델은 명령 모델을 주로 사용한다.

### 5.2 검색을 위한 스펙
- 검색 조건을 다야하게 조합해야 할 때 사용할 수 있는것이 스펙이다. 스펙은 애그리거트가 특정 조건을 충족하는지 검사할 때 사용하는 인터페이스다.

### 5.4 리포지터리/DAO에서 스펙 사용하기
- 스펙을 충족하는 엔티티를 검색하고 싶다면 findAll() 메소드를 사용하면 된다. findAll 메소드느 스펙 인터페이스를 파라미터로 갖는다.
```java
public interface OrderSummaryDao extends Repository<OrderSummary, String> {
    List<OrderSummary> findAll(Specification<OrderSummary> spec);
}
```
- 이 메서드를 사용하는 쪽 코드는 아래와 같다.
```java
Specification<OrderSummary> spec = new OrdererIdSpec("user1");
List<OrderSummary> results = orderSummaryDao.findAll(sepc);
```

### 5.5 스펙 조합
- 스프링 데이터 JPA가 제공하는 스펙 인터페이스는 스펙을 조합하는 메소드를 제공하는데 and, or 메소드이다.
- and 메소드는 두 스펙을 모두 충족하는 스펙을 생성하고 or 메소드는 둘 중 하나 이상 충족하는 스펙을 생성한다.
- 이외에도 not() 메소드, null 여부 검사를 위해 where() 메소드를 사용할 수 있다.

### 5.6 정렬 지정하기
- 스프링 데이터 JPA는 두 가지 방법을 사용해서 정렬을 지정할 수 있다.
  - 메소드 이름에 OrderBy를 사용
  - sort를 인자로 정렬
- 이 중 JPA Repository에 이름에 OrderBy를 사용하는 방법은 간단하지만 정렬 기준 프로퍼티가 두 개 이상이면 메서드 이름이 길어지는 단점이 있다.
- 또한 **메소드 이름으로 정렬 순서가 정해지기 때문에** 동적으로 정렬 순서를 변경할 수 없다. 이럴 때는 sort 타입을 사용하면 된다.
```java
public interface OrderSummaryDao extends Repository<OrderSummary, String> {
    List<OrderSummary> findAllOrdererId(String ordererId, Sort sort);
}
```
- 위에서 find 메소드에 Sort 파라미터를 추가했다. 스프링 데이터 JPA는 파라미터로 받은 Sort를 사용해 정렬쿼리를 만든다.
```java
Sort sort = Sort.by("number").ascending();
List<OrderSummary> results = orderSummaryDao.findByOrdererId("user1", sort);
```

### 5.7 페이징 처리하기
- 전체 데이터 중 일부만 보여주는 페이징 처리는 기본이다. 스프링 데이터 JPA는 이를 위해 Pageable 타입을 이용한다.
- Sort 타입과 마찬가지로 파라미터로 전달하면 페이징을 자동으로 처리해준다.
```java
public interface OrderSummaryDao extends Repository<OrderSummary, String> {
    List<OrderSummary> findAllOrdererId(String ordererId, Pageable pageable);
}
```
- Pageable은 인터페이스므로 실제 타입 객체는 PageRequest를 사용한다.
```java
PageRequest pageRequest = PageRequest.of(1, 10);
List<OrderSummary> results = orderSummaryDao.findByOrdererId("user1", pageRequest);
```
- Page 타입을 사용하면 데이터 목록 뿐만 아니라 전체 개수도 구할 수 있다.
```java
public interface OrderSummaryDao extends Repository<OrderSummary, String> {
  Page<OrderSummary> findAllOrdererId(String ordererId, Pageable pageable);
}
```

## 6장. 응용 서비스와 표현영역

### 6.1 표현 영역과 응용 영역
- 사용자와 도메인을 연결해 주는 역할을 하는 것이 응용 영역과 표현 영역이다.
- 응용 서비스의 메서드가 요구하는 파라미터와 표현 영역이 전달받은 데이터는 일치하지 않을 수 있다. 이런 경우 표현 영역은 응용 영역이 
요구하는 객체나 데이터를 생성한 뒤 응용 영역의 메소드를 호출한다.

### 6.2 응용 서비스의 역할
- 응용 서비스의 주요 역할은 도메인 객체를 사용해서 사용자의 요청을 처리하는 것으로 표현 영역의 입장에서 보면 응용 서비스는
도메인 영역과 표현 영역을 연결해주는 창구 역할을 한다.
- 응용 서비스가 복잡하다면 응용 서비스에서 도메인 로직의 일부를 구현할 가능성이 높다. 응용 서비스가 도메인 로직을 일부 구현하면
코드 중복, 로직 분산 등 코드 품질에 안 좋은 역할을 미친다.
- 도메인의 상태나 값을 체크하는 것은 도메인의 핵심 로직이기 때문에 다음 코드처럼 응용 서비스에서 이 로직을 구현하면 안 된다.
```java
public class ChangePasswordService {
    public void changePassword(String memberId, String oldPw, String newPw) {
        Member member = memberRepository.findById(memberId);
        if (!passwordEncoder.matched(oldPw, newPw)) {
            throw new IllegalArgument();
        }
        member.changePassword();
    }
}
```
- 도메인 로직을 도메인 영역과 응용 서비스에 분산해서 구현하면 여러 응용 서비스에서 동일한 도메인 로직을 구현할 가능성이 높아진다.
애초에 도메인 영역에 암호확인 기능을 구현하면 응용 서비스는 그 기능을 사용하기만 하면 된다.

### 6.3 응용 서비스의 구현
- 응용 서비스는 표현 영역과 도메인 영역을 연결하는 매개체 역할을 하는데 이는 마치 디자인 패턴의 파사드와 같은 역할을 한다. 
- 응용 서비스는 크게 다음 두 가지 방법 중 한 가지 방식으로 구현한다.
  - 한 응용 서비스에 도메인의 모든 기능 구현하기
  - 구분되는 기능별로 응용 서비스를 여러개 구현하기
- 위의 예시에서 한 응용 서비스에 모든 기능을 구현한다는 것은 회원가입/수정/조회/삭제 등의 기능을 한 서비스에 구현한다는 뜻이다.
  - 이는 코드 중복을 제거하기 쉬운 장점이 있지만 코드 크기가 커지면 연관성이 적은 클래스가 함께 위치할 가능성이 많아진다.
  - 또한 한 클래스에 코드가 모이기 시작하면 엄연히 분리하는게 좋은 상황임에도 습관적으로 기존에 존재하는 클래스에 코드를 끼워 넣게 된다.
- 구분되는 기능별로 서비스를 구현하면 클래스 개수는 많아지지만 코드 품질을 일정 수준으로 유지하는데 도움이 된다. 각 기능마다 동일한 로직을
구현할 경우(존재하는 도메인을 찾는 경우) Helper 클래스에 로직을 구현해서 코드가 중복되는 것을 방지할 수 있다.

#### 6.3.2 응용 서비스와 인터페이스
- 인터페이스가 필요한 몇 가지 상황이 있는데 그 중 하나는 구현 클래스가 여러개인 경우다. 구현 클래스가 다수 존재하거나
런타임에 구현 객체를 교체해야 할 때 인터페이스를 유용하게 사용할 수 있다.
- 하지만 응용 서비스는 런타임에 교체하는 경우는 거의 없고 한 응용 서비스의 구현 클래스가 두 개인 상황도 드물다.
- 인터페이스와 클래스를 따로 구현하면 소스 파일만 많아지고 간접 참조가 증가한다. 따라서 인터페이스가 확실하게 필요하기 전까진
응용 서비스에 대한 인터페이스를 작성하는 것이 좋은 선택은 아니다.

#### 6.3.3 메서드 파라미터 값 리턴
- 응용 서비스는 표현 영역으로 부터 개별 파라미터를 전달받을 수도 있고 별도의 데이터 클래스를 전달받을 수도 있다. 응용 서비스에
데이터로 전달할 요청 파라미터가 두 개 이상 존재하면 데이터 전달을 위한 별도 클래스를 사용하는 것이 편리하다.

## 6.4 표현 영역
- 표현 영역의 책임은 크게 다음과 같다.
  - 사용자가 시스템을 사용할 수 있는 화면을 제공한다. 사용자는 표현 영역이 제공하는 폼에 알맞은 값을 입력하고 폼을 표현 영역에 전송한다.
  - 사용자의 요청에 맞게 응용 서비스에 필요한 데이터를 전달하고 반환한다. 정상적인 처리 외에도 응용 서비스에서 예외가 발생하면 에러 코드를
  설정하는데 표현 영역의 뷰는 이 에러에 따른 알맞은 처리(메시지 출력 같은)를 하게 된다.

## 6.5 값 검증
- 값 검증은 표현 영역과 응용 서비스 두 곳에서 처리가 가능하지만 원천적으로 모든 값의 검증은 응용 서비스에서 처리한다.
- 실무에선 주로 표현 영역과 응용 서비스가 값 검사를 나눠서 수행한다.
  - 표현 영역은 필수 값, 값의 형식, 범위 등을 검증
  - 응용 서비스는 데이터의 존재 유무와 같은 논리적 오류를 검증

## 6.7 조회 전용 기능과 응용 서비스
- 서비스에서 아래와 같이 단순히 조회 전용 기능을 리턴한다면 트랜잭션이 필요하지도 않다. 이런 경우 굳이 서비스를 만들 필요 없이
**표현 영역에서 바로 조회 전용 기능을 사용**해도 **문제가 없다**.
```java
public class AService {
    public List<A> getAList(String id) {
        return aDao.selectAll(id);
  }
}

public class AController {
  @RequestMapping("/a")
  public List<A> getAList() {
    // id 구함
    return aDao.selectAll(id);
  }
}
```
- 응용 서비스를 항상 만들었던 개발자는 컨트롤러에서 응용 서비스 없이 조회 전용 기능에 접근하는게 이상하게 느껴질 수도 있다.
하지만 응용 서비스가 사용자 요청 기능을 실행하는데 별다른 기여를 하지 못한다면 굳이 서비스를 만들지 않아도 된다.

## 7. 도메인 서비스

### 7.1 여러 애그리거트가 필요한 기능
- 도메인 영역의 코드를 작성하다 보면 한 애그리거트로 기능 구현할 수 없을 떄가 있다. 대표적으로 결제 금액 계산 로직이다.
- 실제 결제 금액을 계산할 때는 상품, 주문, 할인 쿠폰 등의 애그리거트가 사용될 수 있다.
- 이 때 실제 결제 금액을 계산해야 하는 주체는 어떤 애그리거트일까?
  - 생각해 볼 수 있는 방법은 주문 애그리거트가 필요한 데이터를 모두 가진 후 계산 책임을 주문 애그리거트에 할당하는 것이다.
  - 이 때 특별 세일로 한달간 2% 추가 할인을 한다고 해보자. 주문 애그리거트가 가지고 있는 구성요소와는 관련이 없음에도
  결제 금액 계산 책임이 주문 애그리거트가 가지고 있다는 이유로 주문 애그리거트의 코드를 수정해야 한다.
- 이렇게 한 애그리거트에 넣기 애매한 기능을 억지로 특정 애그리거트에 구현하면 안된다. 억지로 구현하면 애그리거트는 코드가 길어지고
외부와 의존성이 높아지며 복잡해지게 된다.

## 7.2 도메인 서비스
- 도메인 서비스는 도메인 영역에 위치한 도메인 로직을 표현할 때 사용하는데 보통 아래 상황에 사용한다.
  - 여러 애그리거트가 필요한 계산로직이나 한 애그리거트에 넣기에는 다소 복잡한 계산 로직
  - 외부 시스템 연동이 필요한 도메인 로직

### 7.2.1 계산 로직과 도메인 서비스

- 애그리거트에 넣기 애매한 도메인 개념을 애그리거트에 억지로 넣기보다는 도메인 서비스를 이용해서 명시적으로 드러내면 된다.
- 엔티티(도메인, 애그리거트)와 도메인 서비스를 비교할 때 다른점은 도메인 서비스는 상태없이 로직만 구현한다는 점이다.
- 할인 금액 계산 로직을 위한 도메인 서비스는 아래와 같이 도메인의 의미가 드러나는 용어를 사용한다.
```java
public class DiscountCalculationService {
    public Money calculateDiscountAmounts(List<OrderLine> orderLines, List<Coupon> coupons, MemberGrade grade) {
        // 계산 로직..
    }
}
```
- 아래와 같이 애그리거트가 도메인 서비스를 사용한다.
```java
public class Order {
    public void calculate(DiscountCalculationService discountCalculationService, MemberGrade grade) {
        Money totalMoney = getTotalMoney();
        discountCalculationService.calculateDiscountAmounts(..);
    }
}
```
- 애그리거트 객체에 도메인 서비스를 전달하는 것은 응용 서비스 책임이다.
```java
public class OrderService {
    private DiscountCalculationService discountCalculationService;
    
    @Transactional
    public Order createOrder() {
      ...
    }
}
```
- **도메인 서비스 객체를 애그리거트에 주입하지 않기**
  - 애그리거트에 도메인 서비스 객체를 파라미터로 전달한다는 것은 애그리거트가 도메인 서비스에 의존한다는 뜻이다.
  - 의존성을 줄이기 위해 DI를 생각할 수 있겠지만 이럴 경우 데이터와 메소드를 이용해 하나의 모델을 표현하는 도메인이 불필요한 필드를
  가지게 되므로 도메인과 도메인 서비스의 관계는 의존성 주입을하지 않는게 좋다.
- 반대로 도메인 서비스의 기능을 실행할 때 애그리거트가 전달되는 경우도 있다.
- 도메인 서비스는 도메인 로직을 수행하지 응용 로직을 수행하진 않는다.
  - 특정 기능이 응용 서비스인지 도메인 서비스인지 감을 잡기 어려운 경우 로직이 애그리거트의 상태를 변경하거나 상태 값을 계산하는지 살펴보자.
  - 예를 들어 계좌 이체 기능은 계좌 애그리거트의 상태를 변경하고 결제 금액 로직은 주문 애그리거트의 금액을 계산한다. 이 둘은 도메인 로직이다.

### 7.2.2 외부 시스템 연동과 도메인 서비스
- 외부 시스템이나 타 도메인과의 연동 기능도 도메인 서비스가 될 수 있다. 예를 들어 설문 조사 시스템과 사용자 역할 관리 시스템이 분리되어 있다고
가정하자. 설문조사 시스템은 설문 조사를 생성할 때 사용자의 권한을 확인해야 한다.
- 시스템 간 연동은 http로 이루어지지만 설문 조사 도메인 입장에서는 사용자가 권한을 가졌는지 확인하는 도메인 로직으로 볼 수 있다.

### 7.2.3 도메인 서비스의 패키지 위치
- 도메인 서비스는 도메인 로직을 표한하므로 도메인과 같은 패키지에 위치한다. 만약 이를 구분하고 싶다면
domain 패키지 하위에 domain.model, domain.service, domain.repository와 같이 하위 패키지를 구분해도 된다.

## 8. 애그리거트 트랜잭션 관리

### 8.1 애그리거트와 트랜잭션
- 운영자와 고객이 동시에 한 주문 애그리거트를 수정하면 애그리거트의 일관성이 깨지게 된다.
- 일관성을 유지하려면 아래 두가지 중 하나를 해야 한다.
  - 운영자가 배송지 정보를 조회하고 상태를 변경하는 동안 고객이 애그리거트를 수정하지 못하도록 한다.
  - 운영자가 배송지 정보를 조회한 이후에 고객이 정보를 변경하면 운영자가 애그리거트를 다시 조회한 뒤 수정한다.
- 위 두가지는 애그리거트의 트랜잭션과 관련이 있다. DBMS가 지원하는 트랜잭션과 함께 애그리거트를 위한 추가적인 트랜잭션 기법이 필요하다.

### 8.2 선점 잠금
- 먼저 애그리거트를 구한 스레드가 애그리거트를 다 사용할 때까지 다른 스레드가 해당 애그리거트를 수정하지 못하도록 한다.
- 운영자 스레드가 선점 잠금 방식으로 주문 애그리거트를 구하면 바로 뒤이어 고객 스레드는 대기 상태가 된다. 운영자 스레드가
배송상태로 변경뒤 트랜잭션을 커밋하면 잠금을 해제한다. 이제 고객 스레드가 구한 애그리거트는 운영자가 배송상태를 변경한 애그리거트다.
이미 배송중 상태이므로 더 이상 주소를 수정할 수 없다는 에러를 보게 된다.
- 선점 잠금은 보통 DBMS가 제공하는데 많은 DBMS가 특정 레코드에 한 스레드만 접근하도록 잠금장치를 제공한다.

### 8.2.1 선점 잠금과 교착상태
- 스레드 1은 A 애그리거트의 잠금을 구하고 스레드 2는 B 애그리거트 잠금을 구한 상태에서 스레드 1이 B 애그리거트 선점 잠금을 시도하고
스레드 2가 A 애그리거트 선점 잠금을 시도하면 이는 교착상태로 빠지게 된다.
- 선점 잠금에 따른 교착상태는 사용자가 많을수록 발생할 가능성이 높다. 이런 문제를 발생하지 않도록 하려면 잠금을 구할때 최대 대기 시간을
지정해야 한다.

### 8.3 비선점 잠금
- 선점 잠금으로 막을 수 없는 상황이 있는데 가령 운영자는 고객의 정보를 이미 조회해서 보는 상태이고 이때 고객이 주소를 변경해버렸다.
이후에 운영자가 브라우저로 가져온 화면에서 배송중 상태로 변경하면 고객이 주소를 변경하기 전의 주소로 배송이 된다.
- 이때는 비선점 잠금이 필요하다. 동시에 접근하는 것을 막는대신 변경한 데이터르 실제 DBMS에 반영하는 시점에 변경 가능 여부를 확인해야 한다.
- 비선점 잠금을 구현하려면 애그리거트에 버전으로 사용할 숫자 타입 프로퍼티를 추가해야 한다.
- 애그리거트를 수정할 때 마다 버전으로 사용할 프로퍼티 값이 1씩 증가하는데 애그리거트 수정시점에 이 버전이 동일할 경우에만 데이터를 수정한다.
- JPA는 @Version 애노테이션으로 비선점 잠금 기능을 지원한다. 다음과 같이 버전으로 사용할 필드에 에너테이션을 붙인다.
```java
@Entity
public class Order { 
    @Version
    private long version;
}
```

## 9. 도메인 모델과 바운디드 컨텍스트

### 9.1 도메인 모델과 경계
- 처음 도메인 모델을 만들때 빠지기 쉬운 함정으로 도메인을 완벽하게 표현하는 단일 모델을 만드는 시도를 하는 것이다.
- 논리적으로 같은 존재처럼 보이지만 하위 도메인에 따라 다른 용어를 사용하는 경우가 있다. 카탈로그 도메인에서는 상품이지만
검색도메인에서는 상품이 아닌 문서로 불리기도 한다. 시스템을 사용하는 사람을 회원 도메인에서는 회원이지만 주문 도메인에서는 주문자라고 부르기도 한다.
- 이렇게 하위 도메인마다 같은 용어라도 의미가 다를수 있기 때문에 한개의 모델로 모든 하위 도메인을 표현하려는 시도는 올바른 방법이 아니다.
- 하위 도메인마다 사용하는 용어가 다르기 때문에 올바른 도메인 모델을 개발하려면 하위 도메인마다 모델을 만들어야 한다. 각 모델은 경계를 가져서
섞이지 않도록 해야하며 이렇게 구분되는 경계를 DDD에서는 **바운디드 컨텍스트**라고 부른다.

### 9.2 바운디드 컨텍스트
- 바운디드 컨텍스트는 모델의 경계를 결정하며 한 개의 바운디드 컨텍스트는 논리적으로 한 개의 모델을 가진다.
- 바운디드 컨텍스트는 용어를 기준으로 구분하는데 카탈로그와 재고 컨텍스트는 서로 다른 용어를 사용하므로 이 용어를 기준으로 컨텍스트를 분리할 수 있다.
- 이상적으로 하위 도메인과 바운디드 컨텍스트가 일대일 관계를 가지면 좋겠지만 현실은 그렇지 않을떄가 많다. 바운디드 컨텍스트는 팀 조직 구조에
따라 결정되기도 한다.

### 9.3 바운디드 컨텍스트
- 바운디드 컨텍스트가 도메인 모델만 포함하는 것은 아니다. 바운디드 컨텍스트는 도메인, 표현, 응용, 인프라 영역까지 모두 포함한다.
쉽게 말해 **도메인 기능을 제공하는데 필요한 모든 요소를 포함**한다.
- 모든 바운디드 컨텍스트를 반드시 도메인 주도로 개발할 필요는 없다. 상품의 리뷰같은 간단한 로직들은 복잡한 도메인 로직을 갖지 않기 때문에
서비스 + DAO를 이용해서 구현해도 된다.
- 각 바운디드 컨텍스트는 서로 다른 구현기술을 사용할 수 있다. 스프링과 JPA를 쓰는 바운디드 컨텍스트가 존재할수도 있고 Netty를 이용해
REST API를 제공하는 바운디드 컨텍스트가 존재할 수도 있다.

### 9.4 바운디드 컨텍스트간 통합
- 쇼핑몰에서 매출을 위해 카탈로그에 개인화 추천기능을 도입하기로 했다. 새로운 팀이 이를 맡기로 가정하면 카타로그 하위 도메인으로
카탈로그를 위한 바운디드 컨텍스트와 개인 추천기능을 위한 바운디드 컨텍스트가 생긴다.
- 이때 카탈로그와 추천 컨텍스트의 도메인 모델은 서로 다르다. 카탈로그는 제품을 중심으로 도메인 모델을 구현하지만 추천은 추천 연산을
위한 모델을 구현한다.
- 카탈로그 시스템에선 추천 도메인 모델에 REST API로 데이터를 받아 제공한다. 이 방식은 두 바운디드 컨텍스트를 직접 통합하는 방법이다.
- 간접적으로 통합하는 방식도 있는데 이떄는 메시지 큐를 사용한다. 카탈로그 바운디드 컨텍스트는 추천 시스템이 필요로 하는 사용자 활동 이력을
메시지 큐에 추가한다. 메시지 큐는 비 동기로 메시지를 처리하기 때문에 카탈로그 바운디드 컨텍스트는 메시지를 큐로 보낸 후 기다리지 않고 바로
자신이 할 일을 이어 작업한다.
- 추천 바운디드 컨텍스트는 큐에서 이런 메시지를 읽어와 추천을 계산하는데 사용할 것이다. 이 말은 두 바운디드 컨텍스트가 사용할 메시지의
데이터 구조를 맞춰야 함을 의미한다. 
- 어떤 도메인 관점에서 모델을 사용하느냐에 따라 두 바운디드 컨텍스트의 구현 코드가 달라질 수 있다. 카탈로그 도메인 관점에서 큐에 저장할
메시지를 생성하면 카탈로그는 그대로 데이터를 메시지 큐에 저장하고 추천 시스템은 이를 받아 자신의 모델에 맞게 메시지를 변환해야 한다.
반대로 추천 시스템이 큐에 저장할 메시지를 넣는다면 카탈로그 측에서 메시지를 변환해야 한다.
- 바운디드 컨텍스트를 마이크로서비스로 구현하면 자연스럽게 컨텍스트 별로 모델이 분리된다. 마이크로 서비스마다 프로젝트를 생성하므로
  바운디드 컨텍스트마다 프로젝트를 만들게 되고 각 바운디드 컨텍스트의 모델이 섞이지 않도록 해준다.

### 9.5 바운디드 컨텍스트간 관계
- 바운디드 컨텍스트 관계 중 가장 흔한 관계는 한쪽에서 API를 제공하고 다른쪽에서 그 API를 호출하는 관계이다.
카탈로그 바운디드 컨텍스트는 추천 상품을 보여주기 위해 추천 바운디드 컨텍스트가 제공하는 REST API를 호출한다.
- 추천 바운디드 컨텍스트는 일종의 공급자 역할을 하고 카탈로그는 서비스를 사용하는 고객 역할을 한다. 이때 이 두 팀은 상호협력이 필수적이다.
- 상류팀의 고객에 하류팀이 다수 존재하면 여러 하류팀의 요구사항을 충족할 수 있는 API를 만들어 제공해야 하는데 대표적인 예가 검색이다.
블로그, 카페, 게시판과 같은 서비스를 제공하는 포털은 각 서비스별로 검색기능을 구현하기 보다는 검색을 위한 전용 시스템을 구축하고
검색 시스템과 각 하류 서비스를 통합한다.
- 두 바운디드 컨텍스트가 같은 모델을 공유하는 경우도 있다. 이 방법은 중복을 줄여주는 장점을 가지는 대신에 두 팀이 밀접한 관계를 유지해야 한다.
만약 밀접하게 두 팀이 움직이지 않는다면 공유 방법의 장점보다 공유 모델로 개발이 지연되고 정체되는 문제가 더 커지게 된다.

### 9.6 컨텍스트 맵
- 개별 바운디드 컨텍스트에 매몰되면 전체를 보지 못할 때가 있다. 전체 비지니스를 볼 수 있는 방법이 필요한데 이게 컨텍스트 맵이다.
- 바운디드 컨텍스트 영역에 주요 애그리거트를 함께 표시하고 오픈 호스트 서비스(OHS)와 안티코렵션 계층(ACL) 표시해 전체 관계를 이해하는데 도움이 되도록 한다.
- 컨텍스트 맵을 그리는 규칙은 따로 없다. 간단한 도형과 선을 이용해 각 컨텍스트를 이해할 수 있는 수준에서 그리면 된다. 컨텍스트 맵은 단순하기 
때문에 화이트보드나 파워포인트 같은 도구를 이용해서 쉽게 그릴 수 있다.

## 10. 이벤트

### 10.1 시스템 간 강결합 문제
- 쇼핑몰에서 구매를 취소하면 환불을 처리해야 한다. 이 때 환불 기능을 실행하는 주체는 주문 도메인 엔티티가 될 수 있다.
- 도메인 객체에서 환불 기능을 위해 도메인 서비스를 파라미터로 전달받는다.
```java
public class Order {
    // 외부 서비스를 실행하기 위해 도메인 서비스를 파라미터로 전달받음
    public void cancel(RefundService refundService) {
        this.state = OrderState.canceled;
        try {
            refundService.refund(getPaymentId());
            this.refundStatus = State.REFUND_COMPLTE;
        } catch (Exception e) {
            //
        }
    }
}
```
- 이 떄 두가지 문제가 발생하는데 첫번째는 외부 서비스가 정상이 아닐 경우 트랜잭션 처리를 어떻게 할지 애매하다는 점이다. 환불 과정에서 익셉션이
발생하면 롤백해야 할까? 일단 커밋해야 할까?
- 두 번째로 성능에 대한 것인데 환불을 처리하는 외부 시스템의 응답 시간이 길어지면 그만큼 대기 시간도 길어진다. 환불 처리 기능이 30초가 
걸리면 주문 취소 기능도 그만큼의 시간이 걸리고 외부 서비스 성능에 직접적인 영향을 받게 된다.
- 또한 주문 로직과 결제 로직이 섞이는 문제가 있으며 주문 도메인 코드를 결제 도메인 때문에 변경할지도 모른다는 상화은 좋은게 아니다.
- 환불시 취소를 통지해야 한다면 취소 기능에서 통지 서비스를 파라미터로 받아 처리해야 하는데 트랜잭션 처리가 더 복잡해지고 영향을 주는 
외부 서비스가 두 개로 늘어나게 된다.
- 지금까지 주문 바운디드 컨텍스트와 결제 바운디드 컨텍스트 간의 강결합 문제를 다뤘다. 이 결합을 없애는 방법이 있는데 바로 이벤트를 사용하는 것이다.
- 특히 비동기 이벤트를 사용하면 두 시스템 간의 결합을 크게 낮출수 있고 한번 익숙해지면 모든 연동을 이벤트와 비동기로 처리하고 싶을 정도로 매력적이다.

### 10.2 이벤트 개요
- 이벤트라는 용어는 과거에 벌어진 어떤 것을 의미한다. 가령 '암호를 변경했음 이벤트', '주문을 취소했음 이벤트'가 발생했다고 할 수 있다.

#### 10.2.1 이벤트 구성요소
- 도메인 모델에 이벤트를 도입하려면 네개의 구성요소인 `이벤트, 이벤트 생성주체, 이벤트 디스패처, 이벤트 핸들러`를 구현해야 한다.
- 도메인 모델에서 이벤트를 생성하는 주체는 엔티티, 밸류, 도메인 서비스와 같은 도메인 객체이다. 
- 이벤트 핸들러는 이벤트 생성자가 만든 이벤트에 담긴 데이터를 이용해 원하는 기능을 실행한다. 예를들어 '주문 취소됨 이벤트'를 전달받은
이벤트 핸들러는 SMS로 취소 사실을 전달할 수 있다.
- 이벤트 생성주체와 이벤트 핸들러를 연결해 주는 것이 이벤트 디스패처다. 이벤트 디스패처 구현방식에 따라 동기나 비동기로 실행하게 된다.

#### 10.2.2 이벤트의 구성
- 이벤트는 발생한 이벤트 정보를 담는데 보통 `이벤트의 종류, 이벤트 발생시간, 추가데이터`를 포함한다.
- 배송지 변경할 때 발생하는 이벤트를 생각해보자. 이 이벤트는 다음과 같이 작성할 수 있다.
```java
public class ShppingInfoChangedEvent {
    private String orderNumber;
    private long timestamp;
    private ShppingInfo newShippingInfo;
    .
    .
}
```
- 클래스 이름을보면 Changed라는 과거 시제를 사용했다. 이벤트는 현재 기준으로 과거에 벌어진 것을 표현하기 때문에 이벤트 이름에는 과거시제를 사용한다.
- 이 이벤트를 발생하는 주체는 Order 애그리거트다. Events.raise()라는 디스패처를 통해 이벤트를 전파하는데 아래와 같이 코드를 작성할 수 있다.
```java
public class Order {
    public void changeShippingInfo(ShppingInfo newShppingInfo) {
        verifyNotYetShipped();
        updateShippingInfo(newShppingInfo);
        Events.raise(new ShippingInfoChangedEvent(number, newShppingInfo));
    }
}
```
- 전달할 이벤트는 이벤트 핸들러가 필요한 데이터를 다 담아야 하지만, 이벤트 자체와 관련없는 정보는 포함할 필요가 없다.

















