# 13 | 비동기 자바스크립트 (part2)

## async와 await

> async와 await 키워드는 ES2017부터 등장한 문법이다.
> <br>이 키워드는 비동기 JavaScript 프로그래밍의 패러다임 전환이라 할만하다.
> <br>async와 await은 프라미스 사용을 극적으로 단순화하며 프라미스 기반의 비동기 코드를 동기적 코드처럼 작성할 수 있게 한다.

비동기 코드는 일반적인 동기적 코드와 같은 방법으로 값을 반환하거나 예외를 일으킬 수 없다.
<br>프라미스를 설계한 이유도 위와 같다.

async와 await은 효율적인 프라미스 기반 코드에서 프라미스를 숨겨, 일기 쉽고 이해하기 쉬운 동기적 코드와 비슷하게 만든다.

### await 표현식

> await 키워드는 프라미스를 받아 반환 값이나 예외로 바꾼다.
> <br>프라미스 객체 p가 있을 때 표현식 `await p`는 `p`가 온아료될 때까지 대기한다.
> <br>`p`가 이행되면 `await p`의 값은 `p`가 이행된 값이다.
> <br>`p`가 거부되면 `await p` 표현식은 `p`와 같은 값을 에러로 발생시킨다.

await은 코드는 여전히 비동기적으로 동작하지만 그 사실이 드러나지 않게 할 뿐이다.

### async 함수

> `await`키워드는 항상 비동기적이므로 중요한 규칙이 있다.
> <br>`await`키워드는 **`async`키워드로 선언된 함수에서만** 사용할 수 있다.

### 여러개의 프라미스 대기

> `async`함수는 프라미스에 기반하므로 여러개의 프라미스를 병렬 작업하고 싶다면 `Promise.all`을 사용하면 된다.

```js
let [value1, value2] = await Promise.all([getJSON(url1), getJSON(url2)]);
```

## 비동기 순회

> ES2018 이전의 프라미스는 `setInterval`이나 웹 브라우저의 `클릭` 이벤트처럼 여러 번 일어날 수 있는 비동기 작업에는 적합하지 않았었다.
> <br>하지만 ES2018에 등장한 `for/await`으로 인해 해결할 수 있게 되었다.

### for/await` 루프

> node 12버전 이상부터는 리더블 스트림을 비동기적으로 이터러블하게 만든다.
> <br>따라서 아래와 같이 `for/await`루프로 스트림의 연속적인 데이터 덩어리를 읽을 수 잇다.

```js
const fs = require('fs');

async function parseFile(filename) {
  let stream = fs.createReadStream(filename, { encoding: 'utf-8' });
  for await (let chunk of stream) {
    parseChunk(chunk);
  }
}
```

`await` 표현식과 마찬가지로 `for/await`루프 역시 프라미스 기반이다.

- 비동기 이터레이터는 프라미스를 생성한다.
- `for/await` 루프느 프라미스가 이행되길 기다렸다가 이행된 값을 루프 변수에 할당하고 루프 바디를 실행한다.
- 이후 이터레이터에서 다른 프라미스를 받아 새 프라미스가 이행되길 기다렸다가 다시 시작한다.

### 비동기 이터레이터

> 비동기 이터레이터는 일반적인 이터레이터와 거의 비슷하지만 중요한 차이가 두 개 있다.

**1. 비동기 이터러블 객체에는 `Symbol.iterator`가 아닌 `Symbol.asyncIterator` 메서드가 있다.**

대표적으로 `for/await`은 일반적인 이터러블 객체와도 호환되지만 비동기 이터러블 객체를 선호하기 때문에 `Symbol.asyncIterator`를 먼저 시도한 후 `Symbol.iterator`를 시도한다.

**2. 비동기 이터레이터의 `next()`메서드는 직접적으로 순회 결과 객체를 반환하는 것이 아닌 순회 결과 객체로 해석되는 프라미스를 반환한다.**

### 비동기 제너레이터

> 비동기 제너레이터는 비동기 함수의 특징과 제너레이터의 특징을 모두 가진다.

일반적인 비동기 함수와 마찬가지로 `await`을 사용할 수 있고, 일반적인 제너레이터와 마찬가지로 `yield`를 사용할 수 있다.
<br>`yield`로 전달하는 값은 자동으로 프라미스가 된다.

문법 또한 `async function`과 `function*`을 조합해 `async function*`을 사용한다.

# 14 | 메타프로그래밍 (part1)

> 일반적인 프로그래밍이 데이터를 조작하는 코드를 작성한다면 메타프로그래밍은 다른 코드를 조작하는 코드를 작성한다.
> <br>JavaScript같은 동적 언어에서는 프로그래밍과 메타프로그래밍이 뚜렷이 구분되지는 않는다.
> <br>하지만 더 정적인 언어에 익숙한 프로그래머에게는 `for/in`루프로 객체 프로퍼티를 순회하는 단순한 작업도 메타프로그래밍으로 느껴 질 수 있다.

## 프로퍼티 속성

> JavaScript 객체의 프로퍼티에는 물론 이름과 값이 있지만, 각 프로퍼티에는 그 프로퍼티가 어떻게 동작하는지 나타내는 세 가지 속성이 있다.

- 쓰기 가능(writable): 프로퍼티 값을 수정할 수 있는지 여부
- 열거 가능(enumerable): 해당 프로퍼티를 열거할 수 있는지 여부
- 변경 가능(configurable): 프로퍼티를 삭제 혹은 프로퍼티 속성을 수정할 수 있는지 여부

이 절은 프로퍼티 속성을 검색하고 설정하는 API를 설명하기 때문에 라이브러리 제작자에게 특히 중요하다.

- 프로토타입 객체에 메서드를 추가하고 내장 메서드처럼 열거 불가로 만들 수 있다.
- 변경하거나 삭제할 수 없는 프로퍼티를 만들어 객체를 `잠금`처리 할 수 있다.

### 데이터 프로퍼티 vs 접근자 프로퍼티

> 데이터 프로퍼티에는 값이 있지만 접근자 프로퍼티에는 게터와 세터 메서드가 있다.

- 데이터 프로퍼티의 속성 4가지: `value`, `writable`, `enumerable`, `configurable`
- 접근자 프로퍼티의 속성 4가지: `get`, `set`, `enumerabel`, `configurable`

### Object.getOwnPropertyDescriptor()

> 이름에서 유추할 수 있듯 `Object.getOwnPropertyDescriptor()`는 자체 프로퍼티에서만 동작한다.
> <br>상속된 프로퍼티 속성을 검색하려면 반드시 명시적으로 프로토타입 체인을 검색해야 한다.

지정된 객체의 프로퍼티 서술자는 `Object.getOwnPropertyDescriptor()`를 호출해 가져온다.

### Object.defineProperty()

> 프로퍼티 속성을 설정하거나 지정된 속성으로 새로운 프로퍼티를 생성하려면 `Object.defineProperty()`를 호출하며 수정할 객체, 생성하거나 변경할 프로퍼티 이름, 프로퍼티 서술자 객체를 전달한다.

둘 이상의 프로퍼티를 한 번에 생성하거나 수정하려면 `Object.defineProperties()`를 사용한다.

`Object.defineProperty`나 `Object.defineProperties()`를 통해 생성이나 수정이 허용되지 않는 프로퍼티를 생성하거나 수정하려 하면 `TypeError`가 일어난다.
<br>확장 불가인 객체에 새로운 프로퍼티를 추가하려 할때도 `TypeError`가 일어난다.

## 객체 확장성

> 확장 가능 속성은 객체에 새로운 프로퍼티를 추가할 수 있는지 결정한다.
> <br>일반적인 JavaScript 객체는 기본적으로 확장 가능이지만 이 절에서 설명하는 함수로 바꿀 수 있다.

- 객체가 확장 가능한지 확인하려면 `Object.isExtensible()`에 객체를 전달하면 된다.
- 객체를 확장 불가로 만들려면 `Object.preventExtensions()`에 객체를 전달하면 된다.

이렇게 하면 객체에 새로운 프로퍼티를 추가하려 할때 `strict`모드에서는 `TypeError`가 발생한다.

객체를 확장 불가로 만든 후에는 다시 확장 가능하게 돌릴 방법이 없다.

### Object.seal()

> `Object.preventExtensions()`와 비슷하지만 객체를 확장 불가로 만드는 동시에 자체 프로퍼티를 모두 변경 불가로 바꾼다.
> <br>따라서 새로운 프로퍼티를 추가할 수 없고 기존 프로퍼티를 삭제할 수도 없지만 쓰기 가능인 기존 프로퍼티를 유지된다.

- 객체가 밀봉됐는지 확인하려면 `Object.isSeales()`에 객체를 전달하면 된다.

### Object.freeze()

> `Object.freeze()`는 객체를 더 단단히 잠근다.
> <br>객체는 확장 불가, 프로퍼티는 변경 불가로 바뀌는 동시에 객체의 자체 데이터 프로퍼티는 모두 읽기 전용으로 바뀐다.

- 객체가 동결됐는지 확인하려면 `Object.isFrozen()`에 객체를 전달하면 된다.

`Object.seal()`과 `Object.freeze()`는 전달받은 객체에만 효과가 있고 프로토타입은 변경하지 않기 때문에 객체를 완전히 잠그려면 프로토타입 체인에 있는 객체 역시 밀봉 또는 동결해야 한다.
