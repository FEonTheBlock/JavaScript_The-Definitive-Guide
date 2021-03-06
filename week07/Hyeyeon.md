# 자바스크립트 완벽 가이드 7주차 학습 - 혜연

## 14장 메타프로그래밍

### 14.3 프로토타입 속성

- 객체의 prototype 속성은 객체가 프로퍼티를 상속하는 부모 객체
- 생성자 함수의 prototype 프로퍼티는 그 생성자로 생성된 객체의 prototype 속성을 가리킴.
- prototype 속성은 객체가 생성될 때 설정됨.
- 객체의 프로토타입은 객체를 Object.getPRototypeOf()에 전달하여 파악할 수 있음.
- 객체가 다른 객체의 프로토타입인지는 isPrototypeOf() 메서드로 파악
- Object.setPrototypeOf()로 객체의 프로토타입을 바꿀 수 있지만 자바스크립트 실행 환경은 객체의 프로토타입이 고정되었다고 가정하고 최적화하므로 사용할 일은 거의 없음.

### 14.4 잘 알려진 심벌

- 잘 알려진 심벌은 자바스크립트 코드에서 객체와 클래스의 저수준 동작을 제어할 때 사용하는 Symbol() 팩토리 함수의 프로퍼티로 저장된 심벌 값

#### 14.4.1 Symbol.iterator와 Symbol.asyncIterator

- 객체나 클래스를 이터러블이나 비동기 이터러블로 만드는 심벌

#### 14.4.2 Symbol.hasInstance

- ES6에서 instanceof의 오른쪽에 [Symbol.hasInstandce] 메서드가 있는 객체가 있으면 왼쪽 값을 인자로 [Symbol.hasInstance] 메서드를 호출하며, 메서드의 반환 값을 불로 변환한 값이 instanceof 연산자의 값

#### 14.4.3 Symbol.toStringTag

- ES6의 Object.prototype.toString()은 인자에서 심벌 이름 Symbol.toStringTag를 가진 프로퍼티를 찾고, 그런 프로퍼티가 존재하면 그 값을 반환하므로 클래스를 직접 정의한 경우에도 classof()같은 함수를 쉽게 사용할 수 있음.

#### 14.4.4 Symbol.species

- Symbol.species를 도입한 설계 의도는 원래 배열의 유용한 서브클래스를 만들 수 있게 하려는 것
  - 형식화 배열 클래스에도 배열 클래스와 같은 방법으로 이 심벌을 사용할 수 있음.
- ES6 전에는 배열과 같은 내장 클래스의 서브클래스를 만들 방법이 없었으나, ES6에서는 class와 extends 키워드를 사용해 내장 클래스를 쉽게 확장할 수 있음.
- ES6 이후의 Array() 생성자에는 심벌 이름 Symbol.species를 가진 프로퍼티가 있고, 이 심벌이 생성자 함수의 프로퍼티 이름으로 사용됨.
- extends로 서브클래스를 만들면 그 서브클래스 생성자는 슈퍼클래스 생성자에서 프로퍼티를 상속하므로 배열의 서브클래스 생성자에는 모두 상속된 Symbol.species 프로퍼티가 있음.
- map()과 slice()처럼 새로운 배열을 생성해 반환하는 메서드는 일반적인 배열을 생성하지 않고, new this.constructor[Symbol.species]()를 호출한 것과 같은 새로운 배열을 생성함.

#### 14.4.5 Symbol.isConcatSpreadable

- 배열 메서드 concat()은 반환될 배열에 사용할 생성자를 정할 때 Symbol.species를 사용하고, concat()의 인자나 this 값이 객체이고 심벌 이름 Symbol.isConcatSpreadable이 있는 프로퍼티가 있다면 이 프로퍼티의 불 값을 사용해 인자를 spread할지 판단함.

#### 14.4.6 패턴 매칭 심벌

- ES6 이후 정규 표현식 인자를 사용해 패턴을 검색하는 문자열 메서드들은 RegExp 객체를 비롯해 심벌 이름을 가진 프로퍼티로 패턴 매칭 동작을 정의하는 모든 객체에서 동작하도록 일반화됨.

#### 14.4.7 Symbol.toPrimitive

- ES6 이후 Symbol.toPrimitive가 객체를 기본 값으로 변환하는 기본 동작을 덮어 쓸 수 있게 하여 클래스 인스턴스가 기본 값으로 변환되는 방법을 완전히 제어할 수 있게 됨.

#### 14.4.8 Symbol.unscopables

- 구식 with문 때문에 배열 클래스에 새로운 메서드를 추가할 때 발생하는 호환성 문제를 해결하기 위해 도입

### 14.5 템플릿 태그

- 값이 함수인 표현식 뒤에 템플릿 리터럴이 있으면 표현식은 함수로 바뀌고, 이 함수를 '태그된 템플릿 리터럴'이라 부름.
  - 태그된 템플릿 리터럴과 함께 사용할 새로운 태그 함수를 정의하는 것도 일종의 메타프로그래밍임.

### 14.6 리플렉트 API

- Reflect 객체는 클래스가 아니며 관련 함수를 모은 집합임.
- ES6에서 도입, 객체와 그 프로퍼티를 반영하는 API로, 새로운 기능을 제공하지는 않지만 필요한 기능을 편리한 API 하나로 묶어 제공함.
- Reflect 함수는 프록시 핸들러 메서드와 1:1로 대응

### 14.7 프록시 객체

- ES6에서 도입한 프록시 클래스는 자바스크립트에서 가장 강력한 메타프로그래밍 기능
- 리플렉트 API를 사용하면 자바스크립트 객체의 기본적인 동작에 직접 접근하여 기본적인 동작을 바꿀 수 있음.
- 기본적인 동작을 직접 구현하며 일반적인 객체에서는 불가능한 방법으로 동작하는 객체를 만들 수 있음.

#### 14.7.1 프록시 불변성

- readOnlyProxy() 함수는 실질적으로 동결된 프록시 객체를 생성함.
- 프록시 클래스는 핸들러 객체나 대상 객체에 모든 동작을 위임한 뒤 결과를 점검해서 중요한 자바스크립트 불변성이 위반되지 않았는지 확인함.
  - 만약 위반이 감지되면 프록시는 동작을 계속 진행하지 않고 TypeError를 일으킴.
