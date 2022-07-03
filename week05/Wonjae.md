# 13 | 비동기 자바스크립트 (part1)

> 비동기란?
> <br>데이터가 들어오거나특정 이벤트가 발생할 때까지 계산을 멈추고 대기하는 일

웹 브라우저의 JavaScript 프로그램은 일반적으로 이벤트 주도적이다.
<br>즉, 프로그램이 실제로 무언가를 실행하기 전에 사용자의 상호작용을 기다린다는 뜻
<br>JavaScript를 사용하는 서버는 일반적으로 네트워크를 통해 클라이언트 요청이 들어온 후에야 작업을 시작한다.

## 콜백과 비동기 프로그래밍

> JavaScript에서 가장 기본적인 비동기 프로그래밍은 콜백함수를 통해 이뤄진다.

### 타이머

> 일정 시간이 지나면 코드를 실행하는 것도 단순한 비동기 프로그램 유형 중 하나이다.

- setTimeout
- setInterval

### 이벤트

> 클라이언트 사이드 JavaScript 프로그램은 거의 대부분 이벤트 주도적이다.
> <br>이들은 미리 지정된 계산을 실행하기보다는 사용자가 뭔가 하길 기다렸다가 그 행동에 반응한다.
> <br>이벤트 주도 JavaScript 프로그램은 지정된 컨텍스트에 지정된 타입의 이벤트를 처리할 콜백 함수를 등록하고, 웹 브라우저는 지정된 이벤트가 일어날 때마다 함수를 호출한다.
> <br>이런 콜백 함수를 이벤트 핸들러, 이벤트 리스너라고 부른다.

- addEventListener
- Element.on~

### 네트워크 이벤트

> 네트워크 요청 역시 JavaScript 프로그래밍의 대표적인 비동기 유형 중 하나이다.

```js
function getCurrentVersionNumber(versionCallback) {
  // 콜백을 인자로 전달받는다.
  // 백엔드의 버전 API에 HTTP 요청을 보낸다.
  let req = new XMLHttpRequest();
  req.open('GET', 'http://www.example.com/api/version');
  req.send();

  // 응답을 받았을 때 호출할 콜백을 등록한다.
  req.onload = function () {
    if (req.status === 200) {
      let currentVersion = parseFloat(req.responseText);
      versionCallback(null, currentVersion);
    } else {
      versionCallback(req.statusText, null);
    }
  };

  req.onerror = req.ontimeout = function (e) {
    versionCallback(e.type, null);
  };
}
```

### 노드의 콜백과 이벤트

> 서버 사이드 JavaScript 환경인 노드는 비동기적으로 만들어져 있으며 많은 API가 콜백과 이벤트를 사용한다.

```js
const fs = require('fs');
let options = {}; // 프로그램에서 사용할 옵션 객체

// 설정 파일을 읽고 콜백 함수를 호출한다.
fs.readFile('config.json', 'utf-8', (err, text) => {
  if (err) {
    // 에러가 있으면 경고를 표시하고 계속 진행한다.
    console.warn('Could not read config file:', err);
  } else {
    Object.assign(options, JSON.parse(text));
  }
  startProgram(options);
});
```

## 프라미스(Promise)

> 프라미스란?
> <br>비동기 작업의 결과를 나타내는 객체이다.
> <br>프라미스의 값을 동기적으로 가져올 수 있는 방법은 존재하지 않는다.
> <br>값이 준비됐을 때 콜백 함수를 호출하도록 프라미스에 요청할 수 있을 뿐이다.

프라미스를 아주 단순히 표현하자면 콜백을 사용하는 새로운 방법이라고 할 수 있다.

### 프라미스가 콜백 패턴에서 개선한 점

- 콜백 헬

콜백 패턴은 콜백 안에 콜백이 그 안에 콜백이 이어지며 가독성을 해치는 콜백 헬이 발생한다.
<br>이런 점을 프라미스에서는 후속처리 메서드를 통해 선형에 가까운 프라미스 체인으로 바꿔준다.

- 에러 처리의 어려움

비동기 함수 또는 비동기적으로 호출된 콜백에서 에러가 발생하면 비동기 처리의 최초 실행자에게 에러를 전달할 방법이 없다.
<br>이런 점을 프라미스에서 `catch`후속 처리 메서드를 통해 표준화했다.

### 프라미스 사용

> 프라미스는 프라미스 객체가 생성된 이후에 일어날 비동기 작업의 결과를 나타낸다.
> <br>프라미스 객체가 반환된 후에 동작이 이뤄지므로 이 동작이 값을 반환할지, 캐치할 수 있는 에러를 발생시킬지 미리 알 수는 없다.

**`then` 후속 처리 메서드**

동기적 작업은 정상적으로 수행되는 경우 호출자에게 결과를 반환한다.
<br>프라미스 기반 비동기 작업은 정상적으로 완료되면 `then`후속 처리 메서드의 첫번째 인자인 함수에 그 결과를 전달한다.

**`catch` 후속 처리 메서드**

동기적 작업에서는 뭔가 잘못되면, 처리할 `catch`절을 만날 때까지 콜 스택을 거슬러 올라가서 전달될 에러를 발생시킨다.
<br>비동기 작업에서는 호출자가 스택에 존재하지 않으므로 에러를 전달할 간단한 방법은 존재하지 않는다.
<br>프라미스 기반 비동기 작업은 에러를 `then`의 두번째 인자인 함수 혹은 `catch`의 첫번째 인자인 함수에 전달한다.

### 프라미스 체인

> 프라미스의 가장 중요한 장점 중 하나는 비동기 작업 시퀀스를 `then`의 체인으로 이어서 콜백 헬을 방지한다는 점이다.

```js
fetch(documentURL)                   // HTTP 요청을 보낸다.
  .then(response => response.json()) // 응답의 JSON 바디를 가져온다.
  .then(document => {                // JSON 분석이 끝나면
    return render(document);         // 문서를 사용자에게 표시한다.
  })
  .then(rendered => {
    cachInDatabase(rendered);        // 로컬 데이터베이스에 캐시한다.
  })
  .catch(error -> handle(error));    // 에러를 처리한다.
```

**잘못된 예**

```js
fetch(testURL).then((res) => {
  res.json().then((profile) => {
    displayUserProfile(profile);
  });
});
```

> 이렇게 프라미스를 콜백처럼 중첩해 사용하는 것은 프라미스의 설계 목적에 부합하지 않는다.

**옳은 예**

```js
fetch(testURL)
  .then((res) => {
    return res.json();
  })
  .then((profile) => {
    displayUserProfile(profile);
  });
```

> 이렇게 프라미스를 연속적인 체인으로 사용하는 것이 설계 목적에 더 부합한다.

```js
fetch().then().then();
```

> 이렇게 표현식 하나에서 메서드를 하나 이상 호출하는 것을 메서드 체인이라 한다.

### 프라미스 해석

> 프라미스가 `해석`된 상태라는 말은 프라미스가 다른 프라미스와 연결된다는 의미이다.
> <br>즉, 이전 비동기 결과의 영향을 받고 있다는 말이다.

```js
function c1(res) {
  let p4 = res.json();
  return p4;
}

function c2(profile) {
  displayUserProfile(profile);
}

let p1 = fetch('/api/user/profile');
let p2 = p1.then(c1);
let p3 = p2.then(c2);
```

1. HTTP 요청을 시작하고 `p1` 반환
2. `p1`에 `c1`을 등록하고 `p2`를 반환
3. `p2`에 `c2`를 등록하고 `p3`를 반환
4. `p1`에 대한 응답이 완료되면 `c1`에서 `p4(p2)`를 반환
5. `p2` 해석
6. 응답 바디를 `JSON`으로 분석
7. 분석된 객체로 `p4`와 `p2`가 이행
8. 분석된 바디로 `c2`를 호출
9. `displayUserProfile`실행

### 프라미스와 에러

> `then`후속 처리 메서드에 두번째 콜백함수를 전달하여 프라미스의 거부에 대한 처리를 할 수 있지만, 일반적으로 콜백 두 개를 전달하는 일은 흔치 않다.
> <br>프라미스 관련 에러는 일반적으로 프라미스 체인에 `catch`후속 처리 메서드를 사용한다.

### 병렬 프라미스

> `Promise.all()`을 사용하면 프라미스를 병렬 실행할 수 있다.

```js
const urls = [
  /* n개 이상의 URL */
];

const promises = urls.map((url) => fetch(url).then((res) => res.text()));

Promise.all(promises)
  .then((bodies) => {
    /* 문자열 배열을 사용할 코드 */
  })
  .catch((e) => console.error(e));
```

**`Promise.all()`의 특징**

- 배열 요소 중 일부가 프라미스가 아니라면 그 값은 이미 이행된 것으로 간주하고 결과 배열에 그대로 복사
- 입력 프라미스 중 하나라도 거부되면 `Promise.all()`이 반환하는 프라미스 역시 거부
- 결과 프라미스는 첫번째로 거부되는 프라미스가 생기는 즉시, 나머지 프라미스가 대기 중이더라도 거부

**`Promise.allSetteled`**

> ES2020에서 등장한 문법으로 해당 문법은 반환된 프라미스를 절대 거부하지 않으며 입력 프라미스 전체가 완료되기 전에는 이행되지 않는다.

```js
Promise.allSettled([Promise.resolve(1), Promise.reject(2), 3]).then((res) => {
  results[0]; // { status: "fulfilled", value: 1 }
  results[1]; // { status: "rejected", reason: 2 }
  results[2]; // { status: "fulfilled", value: 3 }
});
```

때때로 다양한 프라미스를 한 번에 실행하되 첫번째로 이행되는 프라미스의 값만 필요할 때도 있다.
<br>이런 경우에는 `Promise.all` 대신 `Promise.race`를 사용하면 된다.
<br>`Promise.race`는 입력 배열에서 처음으로 이행/거부되는 프라미스와 함께 이행/거부되는 프라미스를 반환한다.(만약 입력 배열에 프라미스가 아닌 값이 있다면 그 값 중 첫번째를 반환한다.)
