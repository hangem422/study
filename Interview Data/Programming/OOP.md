# 객체지향(OOP)란 무엇인가?

컴퓨터 프로그래밍의 패러다임 중 하나인 절차지향 언어에서는 각 명령어의 실행 순서를 기반으로 동작합니다. 하지만 **객체지향 프로그래밍**(Obejct-Oriented Programming)은 데이터와 이를 처리하는 루틴들이 하나의 독립된 객체로 동작합니다. 이를 통해 재사용성 증가라는 이점이 생기며 큰 프로젝트들에 많이 사용됩니다.

## 1. 특징

### 1.1 추상화 (Abstraction)

- 대상의 특성 중 불필요한 부분을 무시하고 필요한 공통점만을 다룹니다. 그래서 현실의 복잡성을 극복하고 목적에 집중 할 수 있도록 합니다.
- 복잡성을 다루기 위한 추상화의 2가지 차원
  1. 사물들 간의 공통점은 취하고, 차이점은 버리는 일반화를 통한 단순화
  2. 중요한 부분의 강조를 위해 불필요한 세부 사항을 제거하는 단순화
- 추상화는 문제 영역과 관점에 의존적이므로 어떤 영역에서는 중요한 속성이 다른 영역에서는 그렇지 않을 수 있습니다. 따라서 원하는 목적이나 기능에 따라 추상화 모델은 서로 다를 수 있습니다.

### 1.2 캡슐화 (Encapsulation)

- 객체 스스로가 자신의 상태를 책임지게 하여, 해당 객체의 역할 수행에 집중할 수 있도록 자율성을 높이는 것입니다.
- 켐슐화의 2가지 관점
  1. **상태와 행위의 캠슐화**(데이터 캡슐화): 객체는 상태와 행동(변수와 함수)를 하나의 단위로 묶는 자율적 실체
  2. **사적인 비밀의 캡슐화**(은닉화): 외부에서 객체의 상태를 마음대로 변경할 수 없도록 보호
- 외부 객체가 메시지를 보냈을 때, 실제 내부에서 상태가 어떻게 바뀌고 어떤 행동을 하는지 스스로만 알 뿐 외부로 노출하지 않습니다. 또한 자신의 상태는 스스로만 변경할 수 있도록 합니다. 요청자는 무엇을 요청하는지만 전달하며, 수신자는 어떻게 처리할지를 결정하면 됩니다.
- 객체의 자율성이 높아질수록 객체간 협력이 유연/간결해집니다. 따라서 재사용성 역시 좋아집니다.

### 1.3 상속성 (Inheritance)

- 부모 클래스의 속성과 기능을 상속받아 동일하게 사용하는 것입니다.
- 새로운 클래스는 상속을 통해 부모 클래스의 속성과 기능을 물려받습니다.
- 범용적인 클래스를 작성한 뒤 상속을 활용하면, 여러 클래스에 중복되는 속성과 기능을 반복적으로 구현하지 않아도 쉽게 적용할 수 있습니다.

### 1.4 다향성 (Polymorphism)

- 동일 요청에 대해 서로 다른 방식으로 응답할 수 있도록 만드는 것입니다.
- 동일한 요청이 들어왔을 때 그것을 구현하는 방법에 차이를 두어 다른 결과를 만들어내는 것을 말합니다.
- 메서드 오버 라이딩과 오버 로딩이라는 형태로 다향성을 지원하며, 언어에 따라 오버라이딩만 지원하기도 합니다.
  - **오버 라이딩**: 상위 클래스에게 상속 받은 동일한 메서드 재정의
  - **오버 로딩**: 동일한 메소드가 매개변수의 차이에 따라 다르게 동작

## 2. 장점

- OOP로 코드를 작성하면 이미 작성한 코드에 대한 **재사용성**이 높습니다.
- 자주 사용되는 로직을 라이브러리로 만들어두면 계속해서 사용할 수 있으며 그 **신뢰성**을 확보할 수 있습니다.
- 라이브러리를 각종 예외상황에 맞게 잘 만들어두면 개발자가 사소한 실수를 하더라도 그 에러를 컴파일 단계에서 잡아낼 수 있으므로 **버그 발생이 줄어듭니다.**
- 또한 내부적으로 어떻게 동작하는지 몰라도 개발자는 라이브러리가 제공하는 기능들을 사용할 수 있기 때문에 **생산성**이 높아지게 됩니다.
- 객체 단위로 코드가 나눠져 작성되기 때문에 **디버깅이 쉽고 유지보수에 용이**합니다.
- 데이터 모델링을 할 때 객체와 매핑하는 것이 수월하기 때문에 **요구사항을 보다 명확하게 파악**하여 프로그래밍 할 수 있습니다.

## 3. 단점

- 객체 간의 정보 교환이 모두 메시지 교환을 통해 일어나므로 실행 시스템에 많은 **Overhead**가 발생하게 됩니다. 하지만 이것은 하드웨어 발전으로 많은 부분 보완되었습니다.
- 바로 객체가 생태를 갖는다는 것입니다. 변수가 존재하고 이 변수를 통해 객체가 **예측할 수 없는 상태**를 갖게 되어 내부에서 버그를 발생시킨다는 것입니다. 이러한 이유로 함수형 패러다임이 주목받고 있습니다.

## 4. 원칙

1. **단일 책임 원칙**(Single Responsibility Principle, SRP): 클래스는 단 하나의 책임을 가져야 하며, 클래스를 변경하는 이유는 단 하나의 이유이어야 합니다.
2. **개방-폐쇄 원칙**(Open-Closed Principle, OCP): 확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 합니다.
3. **리스코프 치환 원칙**(Liskov Subsituation Principle, LSP): 상위 타입의 객체를 하위 타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 합니다.
4. **인터페이스 분리 원칙**(Interface Segregation Principle, ISP): 인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 합니다.
5. **의존 역전 원칙**(Dependecy Inversion Principle, DIP): 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안됩니다.

## 5. 정의

### 5.1 오퍼레이션

객체 지향의 가장 기본은 객체이며, 객체의 핵심은 기능을 제공하는 것입니다. 실제로 객체를 정의할 때 사용하는 것은 객체가 제공해야할 기능이며, 객체가 내부적으로 어떤 데이터를 갖고 있는 자료는 정의되지 않습니다. 이러한 기능을 오퍼레이션이라고 부릅니다. 즉, 객체는 오퍼레이션으로 정의가 됩니다.

### 5.2 시그니처

객체 지향으로 설계하기 위해서는 **오퍼레이션 사용법**을 알아야 합니다. 오퍼레이션의 사용법은 다음 세 가지로 구성됩니다.

1. 기능 식별 이름
2. 파라미터 및 파라미터 타입
3. 기능 실행 결과 값 및 타입

이 세가지를 시그니처라고 부릅니다.

### 5.3 인터페이스

객체가 제공하는 모든 오퍼레이션 집합을 객체의 인터페이스라고 부릅니다. JAVA 언어에서의 인터페이스가 아니라, 객체를 사용하기 위한 명세를 의미한다고 보면 됩니다.

### 5.4 메시지

오퍼레이션의 실행을 요청하는 것을 메시지를 보낸다고 표현합니다. JAVA에서 메서드를 호출하는 것이 메시지를 보내는 과정에 해당됩니다.

### 5.5 책임

객체가 자신이 제공하는 기능으로 정의된다는 것은 객체마다 자신만이 제공할 수 있는 기능에 대한 책임이 있다는 것입니다. 객체가 갖는 책임의 크기는 적을 수록 좋습니다. 즉, 객체가 제공하는 기능의 개수가 적은 것이 좋은 겁니다. 한 객체에 많은 기능이 포함되면, 그 기능과 관련된 데이터들도 한 객체에 모두 포함됩니다. 이 경우, 객체에 정의된 많은 오퍼레이션들이 데이터들을 공유하는 방식으로 프로그래밍 되는데, 절차지향 방식과 다를바가 없습니다. 따라서 책임의 크기는 적을수록 유연해집니다. 이 것을 **단일 책임 원칙**(Single Responsibility Principle)이라고 합니다.

### 5.6 의존성

한 객체가 다른 객체를 이용한다는 것은, 실제 구현에서는 한 객체의 코드에서 다른 객체를 생성하거나 다른 객체의 메서드를 호출한다는 것을 의미합니다. 의존의 영향은 꼬리에 꼬리를 문 것처럼 전파됩니다. 이러다가 변경한 여파가 다시 자기 자신까지 변화시킬 수 있는데, 이것을 순환 의존이라고 합니다. 이것을 해결하기 위해 사용하는 방법을 **의존 역전 원칙**(Dependency Inversion Principle)라고 합니다.

## 6. 출처

- [객제치향(OOP)란 무엇인가? - goodgid](https://goodgid.github.io/What-is-OOP/)
- [Interview_Question_for_Beginner - JaeYeopHan](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Development_common_sense)
