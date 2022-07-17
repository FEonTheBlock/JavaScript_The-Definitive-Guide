# 정민 15.3까지 학습(다시 수정)

## 웹 프로그래밍 기본

### script src 속성의 장점

- JS코드를 HTML파일에서 제거해 단순화한다. 즉, '내용'과 '동작'을 분리한다.
- 여러 개의 웹 페이지에서 같은 JS 코드를 공유시, src 속성을 쓰면 코드 하나만 관리해도 된다.
- JS 코드를 여러 페이지에서 공유한다면 한 번만 내려받으면 된다.(다른 페이지는 브라우저 캐시에서 가져옴)
- src 속성은 임의의 URL을 값으로 받으므로, 한 서버에 있는 JS 프로그램이나 웹 페이지가 다른 웹 서버에 있는 코드를 가져올 수 있다.

### 스크립트 타입 지정

웹 초기에는 JS가 아닌 다른 언어가 쓰일 것을 염두하여 `<script>` 태그에 `language='javascript'`, `type='application/javascript'` 같은 속성을 추가했다.

> 그러나 이제는 불필요하며, JS는 웹의 기본 언어이자 유일한 언어이다.

`<script>` 태그에 type 속성을 사용하는 경우는 단 두 가지 뿐이다.

- 스크립트가 모듈일 때(`type=module`)
- 웹 페이지에 데이터를 가져오지만 표시하지는 않을 때(`type=text/x-custom-data` 같은 임의의 값을 지정하면 JS 코드로 실행되지 않는다. 문서의 트리에는 남아있고, text 프로퍼티로 그 데이터를 가져올 수 있다.)

### document.write 메서드가 성능에 영향을 주는 이유

JS 코드가 문서 콘텐츠에 영향을 주는 방법으로 `document.write()` 메서드를 사용해 스크립트 위치에서 HTML 텍스트를 주입할 수 있었다. 그러나 이런식으로 동작한다는 것은 HTML 파서가 `<script>` 요소를 만날 때마다 반드시 그 스크립트를 실행해 **HTML을 출력하지 않음을 확인한 후에야 문서 분석과 렌더링을 재개**할 수 있다는 의미이다.

> 이런 방식은 **웹 페이지의 분석과 렌더링 속도를 심하게 저해**한다!

### 웹 브라우저의 전역 객체

전역 객체는 두 가지 임무를 수행한다.

- 내장 타입과 함수 정의
- 현재 웹 브라우저 창을 나타내기도 하고, 그 창의 브라우징 히스토리(history), 너비같은 프로퍼티(innerWidth)를 정의하기도 한다.

> 전역 객체와 관련된 기능을 사용할 때는 앞에 `window.`를 붙이는 편이 좋다.

### 클라이언트 사이드 자바스크립트 스레드 모델

JS는 싱글 스레드 언어이므로 프로그래밍이 훨씬 단순하다. 락, 교착상태, 경합 조건을 걱정할 필요가 없다. 그러나 **웹 워커**라는 사용자 인터페이스를 멈추지 않으면서 실행된느 백그라운드 스레드가 존재한다.

> 이를 통해 **웹 플랫폼은 동시성을 구현**한다.

웹 워커 스레드에서 실행되는 코드는 문서 콘텐츠에 접근할 수 없고, 오로지 다른 스레드와 비동기 메시지 이벤트를 통해서만 통신한다.

### 클라이언트 사이드 JS 타임라인

JS 프로그램은 스크립트 실행 단계에서 이벤트 처리 단계로 넘어간다. 그 과정을 한번 살펴보자.

1. 웹 브라우저가 Document 객체를 생성하고 웹 페이지 분석을 시작한다. 이 단계에서 `document.readyState` 프로퍼티의 값은 `loading`이다.

2. HTML 파서가 async, defer, type="module" 속성이 없는 `<script>` 태그를 만나면 스크립트 태그를 문서에 추가하고 스크립트를 실행한다. 이 스크립트는 동기적으로 실행되고 HTML 파서는 일시적으로 중지된다. 이런 스크립트는 `document.write()` 사용이 가능하다.

3. 파서가 async 속성이 있는 `<script>` 요소를 만나면 스크립트 텍스트를 내려받기 시작하고, 모듈이라면 가져오는 모듈 역시 재귀적으로 내려받아 문서 분석을 계속한다. 이러한 비동기 스크립트는 `document.write()` 메서드를 사용해서는 안된다.

4. 문서 분석이 완전히 끝나면 `document.readState` 프로퍼티가 `interactive`로 바뀐다.

5. defer 속성이 있는 스크립트, async 속성이 없는 모듈 스크립트는 문서 순서대로 실행된다.

6. 브라우저가 Document 객체에서 `DOMContentLoaded` 이벤트를 일으킨다. 이 이벤트는 **스크립트 단계를 두 번째 단계로 전환**한다.

7. 이 시점에서 문서 분석은 완전히 끝났지만 브라우저는 여전히 이미지 같은 콘텐츠를 기다리고 있을 수 있다. 콘텐츠 로딩이 끝나고 async 스크립트 로딩과 실행도 끝나면 `document.readyState` 프로퍼티는 `complete`로 바뀌고 웹 브라우저는 윈도우 객체에서 load 이벤트를 일으킨다.

8. 이제부터 사용자의 입력 이벤트, 네트워크 이벤트, 타이머 종료 등에 의해 이벤트 핸들러가 비동기적으로 호출된다.

### 프로그램 입출력

전역 `navigator` 프로퍼티를 통해 웹 브라우저, 운영 체제와 그 기능에 접근할 수 있다. 예를 들어 `navigator.userAgent`는 웹 브라우저를 식별하는 문자열이고 `navigator.language`는 사용자가 선호하는 언어이며 `navigator.hardwareConcurrency`는 웹 브라우저가 사용할 수 있는 논리적 CPU의 개수이다. 마찬가지로 전역 `screen` 프로퍼티의 `screen.width`, `screen.height` 프로퍼티를 통해 사용자의 디스플레이 크기에 접근할 수 있다.

## 이벤트

클라이언트 사이드 JS 프로그램은 **비동기적인 이벤트 주도 프로그래밍 모델**을 사용한다.

### addEventListener의 3번째 인수

```js
document.addEventListener("click", handleClick, {
  capture: true,
  once: true,
  passive: true,
});
```

위 예제에서 3번째 인수안에 있는 객체들을 한번 살펴보자.

- capture: 캡처 프로퍼티가 true이면 이벤트 핸들러는 캡처링 핸들러로 등록된다. 이 프로퍼티가 false이거나 생략됐다면 핸들러는 캡처링을 사용하지 않는다.
- once: 프로퍼티가 true이면 이벤트 리스너는 한 번 호출된 뒤 자동으로 제거된다. 이 프로퍼티가 false이거나 생략됐다면 핸들러는 절대 자동으로 제거되지 않는다.
- passive: 프로퍼티가 true이면 이벤트 핸들러는 절대 `preventDefault()`를 호출해서 기본 동작을 취소하지 않는다. 이 옵션은 모바일 장치의 터치 이벤트에서 특히 중요한데, `touchmove` 라는 이벤트의 이벤트 핸들러가 브라우저의 기본 스크롤을 방해하면 브라우저는 부드러운 스크롤 동작을 구현할 수 없기 때문이다.

### 브라우저의 기본 동작을 막는 방법

`preventDefault()` 메서드를 사용하는 것이 표준 방법이다. 핸들러에 `return false;`를 함으로 기본 동작을 막을 수 있지만, 표준 방법을 사용하는 것이 더 좋다.

### 커스텀 이벤트 전달

JS 객체에 `addEventListener()` 메서드가 있다면 그 객체는 '이벤트 대상'이므로 `dispatchEvent()` 메서드 또한 가지고 있다. `CustomEvent()` 생성자로 이벤트 객체를 생성하고 `dispatchEvent()`에 전달할 수 있다. `CustomEvent()`의 첫 번째 인자는 이벤트 타입을 나타내는 문자열이고 두 번째 인자는 이벤트 객체의 프로퍼티를 지정하는 객체이다.

예시

```js
// 작업 중임을 UI에 알리는 커스텀 이벤트 전달
document.dispatchEvent(new CustomEvent("busy", { detail: true }));

// 네트워크 동작
fetch(url)
  .then(handleNetworkResponse)
  .catch(handleNetworkError)
  .finally(() => {
    document.dispatchEvent(new CustomEvent("busy", { detail: false }));
  });

document.addEventListener("busy", (e) => {
  if (e.detail) {
    showSpinner();
  } else {
    hideSpinner();
  }
});
```

## 문서 스크립트

### 미리 선택된 요소

역사적인 이유로 Docuemnt 클래스에는 특정 노드에 접근하는 단축 프로퍼티가 있다. 예를 들어 images, forms, links 프로퍼티로 문서에 존재하는 `<img>`, `<form>`, `<a>` 요소에 쉽게 접근할 수 있다. 단, `<a>` 태그에 href 속성이 있어야 한다. 이 프로퍼티들은 HTMLCollection 객체를 참조하고, document.forms 프로퍼티를 쓰면 `<form id="address">` 태그에 다음과 같이 접근할 수 있다.

```
document.forms.address;
```

### append() vs appendChild()

`append()`는 인자를 개수 제한 없이 받는다. 또한 Node 객체 또는 문자열을 받는다. 문자열 인자는 자동으로 Text 노드로 변환된다. 그러나 `appendChild()`는 오로지 node 객체만 자식 요소로 추가할 수 있고, 한번에 오직 하나의 노드만 추가할 수 있다.

![](https://velog.velcdn.com/images/hustle-dev/post/c6e08770-e41b-494e-b107-6376fe217d96/image.png)

출처: https://webruden.tistory.com/634

> 모던 자바스크립트 Deep Dive에선 `appendChild()` 메서드만 나와있어 이것을 주로 썻었는데 앞으로 `append()`라는 메서드를 알았으니 이를 사용해야겠다. 새롭고 유용한것을 배워가는 것 같아 기분이 좋다.
