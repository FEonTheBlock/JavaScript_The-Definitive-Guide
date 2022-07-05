# 정민 6주차 학습(14.2 까지)

## 13장

https://velog.io/@hustle-dev/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C-%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8

## 14장(14.2 까지)

## 프로퍼티 속성

이곳에서 말하는 **데이터 프로퍼티(`value`, `writable`, `enumerable`, `configurable`)과 접근자 프로퍼티(`get`, `set`, `enumerable`, `configurable`)** 모두 모던 자바스크립트 딥 다이브에서 다룬 내용이어서 크게 어려운 부분은 없었다.

다만, `getOwnProepertyDescriptor()`라는 메서드는 자체 프로퍼티에만 동작하여 상속된 프로퍼티 속성을 검색할 땐, `Reflect.getOwnPropertyDescriptor()`함수를 참고하라.

`Object.assign()`은 열거 가능한 프로퍼티와 값은 복사할 수 있지만 프로퍼티 속성은 복사할 수 없다.

> 따라서 이 경우 책의 예제처럼, 대상 객체에 복사되는 것은 **게터 메서드가 반환하는 값이지 게터 메서드 자체가 아니다!**

## 객체 확장성

확장 가능 속성은 객체에 새로운 프로퍼티를 추가할 수 있는지 결정한다.

- `Object.isExtensible()`
- `Object.preventExtensions()`

> 두 개의 메서드 사용하고, 일단 확장 불가로 만든 후에는 다시 확장 가능으로 되돌릴 방법 X. 프로토타입에 추가하는 것은 그대로 상속 가능

`Object.seal()`과 `Object.freeze()`는 전달받은 객체에만 효과가 있고, 그 객체의 프로토타입은 변경하지 않는다.

> 그리고 이 함수들 모두 전달받은 객체를 반환하므로 중첩해서 호출이 가능(`Object.preventExtensions()`도 마찬가지)

더하여 동결된 객체(`Object.freeze()`를 사용한 객체)는 JS 테스트 스위트를 방해할 수 있다.

> 테스트 스위트가 무엇인지는 궁금함..
