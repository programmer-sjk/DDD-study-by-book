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
