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
