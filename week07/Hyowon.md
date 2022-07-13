## 14.3 프로토타입 속성

- 객체의 프로토타입은 객체를 Object.getPrototypeOf()에 전달해서 파악할 수 있다
- 객체가 다른 객체의 프로토타입인지(또는 프로토타입 체인에 속해 잇는지)는 isPrototypeOf() 메서드로 파악한다
- Object.setPrototypeOf()로 객체의 프로토타입을 바꿀 수 있다
    - 이 메서드는 사용할 일은 거의 없다
    - 자바스크립트 실행 환경은 객체의 프로토타입이 고정됐다고 가정하고 적극적으로 최적화한다
    - 따라서 이 메서드를 사용하여 변경된 객체를 사용한 코드는 훨씬 느리게 동작할 수 있다

## 14.4 잘알려진 심벌

- 심벌 타입은 웹에 이미 배포된 코드와 호환성을 유지하면서 자바스크립트를 안전하게 확장하려는 목적으로 es6에서 추가되었다

### 14.4.1 Symbol.iterator, Symbol.asyncIterator

### 14.4.2 Symbol.hasInstance

### 14.4.3 Symbol.toStringTag

- es6의 Object.prototype.toString()은 인자에서 심벌 이름 Symbol.toStringTag를 가진 프로퍼티를 찾고, 그런 프로퍼티가 존재하면 그 값을 반환한다

### 14.4.4 Symbol.species

### 14.4.5 Symbol.isConcatSpreadable

- Symbol.isConcatSpreadable이 있는 프로퍼티가 있다면 이 프로퍼티의 불 값을 사용해 인자를 분해할지 판단한다
- 그런 프로퍼티가 없다면 이전 버전과 마찬가지로 Array.isArray()를 사용한다

### 14.4.6 패턴 매칭 심벌

### 14.4.7 Symbol.toPrimitive

### 14.4.8 Symbol.unscopables

## 14.5 템플릿 태그

- 값이 함수인 표현식 뒤에 템플릿 리터럴이 있으면 표현식은 함수로 바뀌고, 이 함수를 태그된 템플릿 리터럴이라고 부른다
- 태그된 템플릿 리터럴과 함께 사용할 새로운 태그 함수를 정의하는 것도 일종의 메타 프로그래밍이다

## 14.6 리플렉트 API

## 14.7 프록시 객체
