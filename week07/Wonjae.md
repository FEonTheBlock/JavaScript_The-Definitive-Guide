# 14 | 메타프로그래밍 (part2)

## 프로토타입 속성

> 객체의 `prototype` 속성은 객체가 프로퍼티를 상속하는 **부모**객체이다.
> <br>생성자 함수의 `prototype` 프로퍼티는 그 생성자로 생성된 객체의 `prototype` 속성을 가리킨다.
> <br> `prototype` 속성은 객체가 생성될 때 설정된다.
> <br> 객체 리터럴로 생성된 객체의 프로토타입은 `Object.prototype`이다.
> <br> `new`키워드로 생성한 객체의 프로토타입은 생성자 함수의 `prototype` 프로퍼티 값이다.
> <br> `Object.create()`로 생성된 객체의 프로토타입은 `Object.create()`의 첫 번째 인자이다.(null일 수 있다.)

초기 브라우저 일부에서는 객체의 `prototype` 속성에 `__proto__`라는 이름을 사용했다.
<br>이 프로퍼티는 오래전에 폐기됐지만 웹에는 여전히 `__proto__`가 쓰인 코드가 많이 남아 있어 `ECMAScript` 표준에서 자바스크립트 실행 환경은 모두 `__proto__`를 지원하도록 정했다.(표준에서 요구한 것은 아니지만 `node.js`도 `__proto__`를 지원한다.)

최신 자바스크립트에서 `__prto__`는 읽고 쓸 수 있는 프로퍼티이고, `Object.getPrototypeOf()`와 `Object.setPrototypeOf()`를 대체할 수도 있지만 그렇게 해서는 안된다.

## 잘 알려진 심벌

> 심벌 타입은 웹에 이미 배포된 코드와 호환성을 유지하면서 자바스크립트를 안전하게 확장하려는 목적으로 `ES6`에 추가됐다.
> <br>`Symbol.iterator`는 잘 알려진(`well-known`) 심벌의 대표적인 예이다.
> <br>잘 알려진 심벌은 자바스크립트 코드에서 객체와 클래스의 저수준 동작을 제어할 때 사용하는 `Symbol()` 팩토리 함수의 프로퍼티로 저장된 심벌 값이다.

### Symbol.iterator와 Symbol.asyncIterator

> 이 두가지 심벌은 객체나 클래스를 이터러블이나 비동기 이터러블로 만든다.

### Symbol.hasInstance

> 이 심벌을 사용하면 필요에 따라 가상의 타입도 `instanceof` 연산자를 써서 범용으로 체크할 수 있다.

### Symbol.toStringTag

> 기본 자바스크립트 객체에서 `toString()` 메서드를 호출하면 `[object Object]`가 반환된다.
> <br>하지만 `Object.prototype.toString()`을 사용하면 아래와 같이 표기된다.

```js
Object.prototype.toString.call([]); // => [object Array]
Object.prototype.toString.call(/./); // => [object RegExp]
Object.prototype.toString.call(() => {}); // => [object Function]
Object.prototype.toString.call(''); // => [object String]
Object.prototype.toString.call(0); // => [object Number]
Object.prototype.toString.call(false); // => [object Boolean]
```

이 기법을 써서 다른 방법으로는 얻을 수 없는 객체의 타입 정보를 얻을 수 있다.

`Object.prototype.toString()`은 인자에서 심벌 이름 `Symbol.toStringTag`를 가진 프로퍼티를 찾고 그런 프로퍼티가 존재하면 그 값을 반환한다.

### Symbol.species

> ES6 이전에는 배열 같은 내장 클래스의 충실한 서브클래스를 만들 방법이 없었다.
> <br>ES6에서는 class와 extends 키워드를 사용해 내장 클래스를 쉽게 확장할 수 있다.

서브클래스의 서브클래스를 만들 때 `Symbol.species`가 유용할 것이다.

### Symbol.isConcatSpreadable

> 배열 메서드 `concat()`은 반환될 배열에 사용할 생성자를 정할 때 `Symbol.species`를 사용하는 메서드 중 하나이다.
> <br>뿐만아니라 `concat()`은 `Symbol.isConcatSpreadable`역시 사용한다.

ES6 이전의 `concat()`은 값이 배열인지 아닌지 판단할 때 `Array.isArray()`를 사용했지만 ES6 이후 알고리즘이 변경되었다.

`concat()`의 인자나 `this`값이 객체이고 심벌 이름 `Symbol.isConcatSpreadable`이 있는 프로퍼티가 있다면 이 프로퍼티의 불 값을 사용해 인자를 분해할지 판단한다.
<br>이 프로퍼티가 없다면 이전 버전과 마찬가지로 `Array.isArray()`를 사용한다.

### 패턴 매칭 심벌

> ES6이후 정규 표현식 인자를 사용해 패턴을 검색하는 문자열 메서드는 `RegExp`객체를 비롯해 심벌 이름을 가진 프로퍼티로 패턴 매칭 동작을 정의하는 모든 객체에서 동작하도록 일반화 되었다.

`match`, `matchAll`, `search`, `replace`, `split`에 대응하는 잘 알려진 심벌 `Symbol.match`, `Symbol.search` 등이 있다.

### Symbol.toPrimitive

> 클래스 인스턴스를 `<` ,`>`와 함께 사용해야 한다면 `[Symbol.toPrimitive]`메서드를 정의하면 된다.

### Symbol.unscopables

> 이 심벌은 구식 `with`문 때문에 발생한 호환성 문제를 해결하기 위해 도입됐다.

`with`문은 객체를 받고 그 객체의 프로퍼티가 변수인 스코프에 있는 것처럼 문의 바디를 실행한다.
<br>배열 클래스에 새로운 메서드를 추가할 때 `with`문 때문에 호환성 문제가 발생했고 이로 인해 기존 코드가 제대로 동작하지 않았다.

이를 해결하기 위해 `Symbol.unscopables`가 도입됐다.

## 템플릿 태그

> 백틱안에 포함된 문자열을 **템플릿 리터럴**이라 부른다.
> <br>값이 함수인 표현식 뒤에 템플릿 리터럴이 있으면 표현식은 함수로 바뀌고, 이 함수를 **태그된 템플릿 리터럴**이라 부른다.

태그 함수는 일반적인 자바스크립트 함수일 뿐 이들을 정의하는 특별한 문법이 있는 것도 아니다.
<br>함수 표현식 뒤에 템플릿 리터럴이 있으면 함수가 호출된다.
<br>첫번째 인자는 문자열 배열이며 그 뒤에 0개 이상의 인자를 붙이고 이 인자들의 값은 타입 제한이 없다.

```js
function html(strings, ...values) {
  // 각 값을 문자열로 변환하고 HTML 특수 문자를 이스케이프 한다.
  let escapes = values.map((v) =>
    String(v)
      .replace('&', '&amp;')
      .replace('<', '&lt;')
      .replace('>', '&gt;')
      .replace('"', '&quot;')
      .replace("'", '&#39;')
  );

  // 이스케이프 결과를 병합한 문자열을 반환한다.
  let result = strings[0];
  for (let i = 0; i < escaped.length; i++) {
    result += escaped[i] + strings[i + 1];
  }
  return result;
}

let operator = '<';
html`<b>x ${operator} y</b>`; // => "<b>x &lt; y</b>
```

## 리플렉트 API

> Reflect 객체는 클래스가 아니며 `Math`객체와 마찬가지로 관련 함수를 모은 집합이다.
> <br>이 함수들은 ES6에서 추가됐으며 객체와 그 프로퍼티를 **반영(`reflect`)**하는 API이다.

`Reflect` 함수는 새로운 기능을 제공하지는 않지만 필요한 기능을 편리한 API하나로 묶어 제공한다.
<br>또한 `Reflect`함수는 프록시 핸들러 메서드와 1:1로 대응한다.

## 프록시 객체

> ES6에서 도입한 프록시 클래스는 자바스크립트에서 가장 강력한 메타프로그래밍 기능이다.
> <br>프록시는 자바스크립트 객체의 기본적인 동작을 바꿀 수 있다.
> <br>프록시 클래스를 사용하면 기본적인 동작을 직접 구현하며 일반적인 객체에서는 불가능한 방법으로 동작하는 객체를 만들 수 있다.

프록시 객체를 생성할 때는 아래와 같이 대상 객체와 핸들러 객체를 제공한다.

```js
let proxy = new Proxy(target, handlers);
```
