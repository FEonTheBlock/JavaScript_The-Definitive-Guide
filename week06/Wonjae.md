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
