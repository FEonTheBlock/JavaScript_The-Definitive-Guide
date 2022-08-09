# 15 | 웹 브라우저의 자바스크립트 (part2)

## CSS 스크립트

> JS는 CSS 스크립트를 통해 문서의 외관과 레이아웃도 수정할 수 있다.

- `display: none`으로 설정하면 요소를 감춘다.
- 요소의 `position`을 `absolute`, `relative`, `fixed`로 설정하고 `top`, `left`에 원하는 좌표를 써서 요소의 위치를 동적으로 바꿀 수 있다.
- `transform`스타일을 써서 요소를 이동, 확대/축소, 회전할 수 있다.
- `transition`스타일을 써서 CSS 스타일에 애니메이션을 적용할 수 있다.

### CSS 클래스

> JS로 문서 콘텐츠의 스타일을 바꾸는 가장 단순한 방법은 HTML 태그의 class 속성에 CSS 클래스를 추가하거나 제거하는 것

### 인라인 스타일

> 모든 `Element` 객체에는 `style` 속성에 대응하는 `style` 프로퍼티가 있다.
> <br>하지만 `style` 프로퍼티는 문자열이 아니라 `CSSStyleDeclaration` 객체이므로 아래와 같이 사용한다.

- `CSSStyleDeclaration` 객체의 스타일 프로퍼티를 사용할 때는 값을 반드시 문자열로 바꿔야한다.
- 스타일시트에서처럼 JS에서도 단위를 써야한다.

```js
$element.style.display = 'block';
$element.style.position = 'absolute';
$element.style.top = '300px';
```

### 계산된 스타일

> 요소의 계산된 스타일은 브라우저가 요소의 인라인 스타일과 모든 스타일시트에서 가져온 적용 가능한 스타일 규칙 전체를 합해 계산한 프로퍼티 값 집합이다.

- 요소의 계산된 스타일을 구할 때는 `window` 객체의 `getComputedStyle()`메서드를 사용하면 된다.

```js
let $title = document.querySelector('#section1title');
let styles = window.getComputedStyle($title);
let beforeStyles = window.getComputedStyle($title, '::before');
```

> `getComputedStyle()`의 반환 값은 지정된 요소에 적용된 스타일 전체를 나타내는 `CSSStyleDeclaration` 객체이다.

- 계산된 스타일 프로퍼티는 읽기 전용이다.
- 계산된 스타일 프로퍼티는 절대값이다.
- 퍼센트나 포인트 같은 상대 단위는 절댓값으로 변환된다.
- 마진 크기나 폰트 크기 등 크기를 나타내는 프로퍼티는 모두 픽셀 값으로 바뀐다.
- 색깔 값 프로퍼티는 `rgb()`나 `rgba()`로 반환된다.

### 스타일시트 스크립트

> JS는 스타일시트 자체도 조작할 수 있다.
> <br>스타일시트는 `<style>`태그 또는 `<link rel="stylesheet">`태그로 HTML문서에 연결된다.
> <br>둘 다 정상적인 HTML태그이므로 id속성을 쓸 수 있고 `document.querySelector()`로 검색할 수 있다.

### CSS 애니메이션과 이벤트

> 웹 브라우저는 트랜지션의 시작과 끝에서 이벤트를 일으키므로 JS를 사용해 CSS 트랜지션의 진행을 모니터링할 수도 있다.

- transitionrun: 트랜지션이 시작될 때
- transitionstart: 시각적변화가 일어남과 동시에 발생
- transitionend: 애니메이션 완료

> CSS 애니메이션도 이벤트를 지원한다.

- animationstart: 애니메이션이 시작할 때
- animationend: 애니메이션이 끝날 때
- animationiteration: 애니메이션이 두 번 이상 반복된다면 마지막을 제외하고 반복마다 발생

## 문서 지오메트리와 스크롤

> 이때까지 문서를 요소와 Text 노드의 추상 트리라고 생각하며 설명했으나, 브라우저는 문서를 창에 렌더링할 때 각 요소의 위치와 크기를 지정해 시각적으로 표현한다.
> <br>웹 애플리케이션에서는 문서를 트리로 취급하여 요소가 화면에 어떻게 렌더링되는지는 신경 쓰지 않아도 될 때가 많으나, 때때로 요소의 위치를 정확히 파악해야 할 때도 있다.

### 문서 좌표와 뷰포트 좌표

> 요소의 위치는 CSS 픽셀로 나타내며 x좌표는 오른쪽으로 갈수록 커지고 y좌표는 아래로 내려갈수록 커진다.

### CSS 픽셀

> 1024 x 768 해상도의 컴퓨터 모니터, 320 x 480 해상도의 터치스크린 폰을 기억할 정도로 나이가 있는 독자라면 `픽셀`이라는 단어를 여전히 하드웨어상의 한 점으로 생각할 수도 있다.
> <br>하지만 최근 4k 모니터와 `레티나` 디스플레이는 해상도가 대단히 높아서 소프트웨어 픽셀과 하드웨어 픽셀을 따로 생각해야한다.

따라서 CSS 픽셀, 즉 클라이언트 사이드 JS 픽셀 하나는 사실 여러 개의 하드웨어 픽셀로 구성될 수 있다.

`window`객체의 `devicePixelRatio`프로퍼티는 소프트웨어 픽셀 하나에 하드웨어 픽셀 몇 개가 사용되는지 나타낸다.
<br>이 값이 2라면 각 소프트웨어 픽셀은 하드웨어 픽셀 2 x 2로 구성된다는 뜻이다.

### 요소의 위치 검색

> `getBoundingClientRect()` 메서드를 호출해 요소의 크기와 위치를 파악할 수 있다.
> <br>이 메서드는 `left`, `right`, `top`, `bottom`, `width`, `height` 프로퍼티가 있는 객체를 반환한다.

### 지정된 위치에 있는 요소 파악

> `document`객체의 `elementFromPoint()`메서드를 사용하면 지정된 위치에 있는 `Element`객체가 반환된다.

### 스크롤

> `window`객체의 `scrollTo()`메서드는 문서 좌표 기준인 x와 y좌표를 받고 이 값을 스크롤 오프셋으로 설정한다.
> <br>즉 브라우저 창을 스크롤해서 지정된 지점을 뷰포트의 좌측 상단 모서리로 만든다.

### 뷰포트 크기, 콘텐츠 크기, 스크롤 위치

- 브라우저창의 뷰포트 크기는 `window.innterWidth`, `window.innerHeight` 프로퍼티로 알 수 있다.
- 요소가 화면에 표시된 크기는 `offsetWidth`, `offsetHeight` 프로퍼티로 알 수 있다.
- 요소의 x, y좌표는 `offsetLeft`, `offsetTop` 프로퍼티로 알 수 있다.
- `clientWidth`, `clientHeight`는 `offsetWidth`, `offsetHeigth`와 비슷하지만 보더가 포함되지 않으며 콘텐츠 영역과 패딩만 포함한다.
- `scrollWidth`, `scrollHeight`는 요소의 콘텐츠 영역과 패딩, 넘치는 콘텐츠를 함한 크기이다.
- `scrollLeft`, `scrollTop`은 요소의 뷰포트 안에 있는 요소 콘텐츠의 스크롤 오프셋이다.

## 웹 컴포넌트

> 최근 웹 앱의 대부분은 순수 HTML로 만들기 보다 리액트나 앵귤러 같은 프레임워크로 만들고, 이런 프레임워크는 사용자 인터페이스를 여러번 사용할 수 있게 만드는 환경을 제공한다.
> <br>웹 컴포넌트란 이런 프레임워크의 대안으로 브라우저에 네이티브로 추가된 기능이다.
> <br>비교적 최근에 웹 표준에 추가된 세가지 기술을 활용해 프레임워크에 의존하지 않고 JS와 새로운 태그만 써서 재사용하기 쉬운 UI 컴포넌트를 만든다.

### 웹 컴포넌트 사용

> 웹 컴포넌트는 JS로 정의되므로 HTML 파일에서 웹 컴포넌트를 사용하려면 컴포넌트를 정의한 JS 파일을 불러와야한다.

```html
<script type="module" src="components/search-box.js">
```

- 웹 컴포넌트는 HTML 태그 이름을 직접 정의하는데, 중요한 점은 이 태그 이름에 반드시 하이픈이 들어가야 한다는 것이다.
- 이 덕분에 HTML 미래 버전에서 하이픈이 없는 새로운 태그를 도입하더라도 웹 컴포넌트와 충돌할 일은 없다.

```html
<search-box placeholder="Search..."></search-box>
```

### HTML 템플릿

> HTML `<template>`태그는 웹 컴포넌트와 밀접한 관련은 없지만 웹 페이지에 자주 등장하는 컴포넌트를 최적화하기에 적합하다.

웹 브라우저는 `<template>`태그와 그 자식 요소를 절대 렌더링하지 않으므로 `<template>`태그는 JS에서만 사용할 수 있다.
<br>이 태그의 목적은 테이블 행 또는 웹 컴포넌트의 내부 구성 요소 같은 기본적인 HTML 구조가 웹 페이지에 여러 번 반복해야 할 때 `<template>`를 써서 한 번만 정의하고 필요한 만큼 JS로 복사해서 쓰는 것이다.

### 커스텀 요소

> 커스텀 요소는 JS 클래스와 HTML 태그 이름을 묶어서 해당 태그가 자동으로 클래스의 인스턴스가 되게 한다.

```js
customElements.define("inline- circle", class InlineCircle extends HlMLElement {
  // 브라우저는 <inline-circle> 요소가 문서에 삽입될 때 이 메서드를 호출합니다.
  // disconnectedCallback() 메서드도 있지만 이 예제에서는 사용하지 않습니다.
  connectedCallback() {
    // 원에 필요한 스타일
    this.style.display = "inline-block";
    this.style.borderRadius "50%";
    this.style.border = "solid black 1px";
    this.style.transfonn = "translateY(l0%)";
    // 이미 정의된 크기가 없다면 현재 폰트 크기에 맞춰 기본 크기를 설정합니다.
    if (!this.style.width) {
      this.style.width "0.8em";
      this.style.height = "0.8em";
    }
  }

  // 정적 프로퍼티 observedAttributes어| ’이벤트’로 등록할 속성을 지정합니다.
  static get observedAttributes() {
    return ["diameter", "color"];
  }

  // 이 콜액은 위에 나열한 속성이 바뀔 때 호출될니다.
  // 커스텀 요소가 처음 파싱될 때도 호출됩니다.
  attributeChangedCallback(name, oldValue, newValue) {
    switch(name) {
      case "diameter":
        // diameter 속성이 바꾸|면 크기를 업데이트합니다.
        this.style.width = newValue;
        this.style.height = newValue;
        break;
      case "color" :
        // color 속성이 바꾸|면 색깔 스타일을 업데이트합니다.
        this.style.backgroundColor = newValue;
        break;
    }
  }

  // 요소 속성에 대응하는 자바스크립트 프로퍼티를 정의합니다.
  // 이틀 게터와 세터는 단순히 대응하는 속성을 가져오고 설정하기만 합니다.
  // 자바스크립트 프로퍼티가 설정되면 해당 프로퍼터는 attributeChangedCallback()을 호출해 요소 스타일을 업데이트합니다.
  get diameter() {
    return this.getAttribute("diameter");
  }
  set diameter(diameter) {
    this. setAttribute("diameter", diameter);
  }
  get color() {
    return this.getAttribute( "color");
  }
  set color(color) {
    this.setAttribute("color", color);
  }
});
```

### 섀도우 DOM

> 섀도우 DOM은 커스텀 요소와 `<div>`, `<span>`, `<body>`... 요소에 섀도우 호스트라 부르는 섀도우 루트를 붙일 수 있게 허용한다.
> <br>섀도우 호스트 요소는 모든 HTML 요소와 마찬가지로 자손 요소와 Text 노드로 구성된 일반적인 DOM 트리의 루트이다.

> 섀도우 DOM의 `섀도우`는 섀도우 루트의 자손인 요소가 `그림자 속에 숨어 있는` 성질을 갖는데서 나온 용어이다.

- 섀도우 루트의 자손은 일반적인 DOM 트리에 속하지 않고, 호스트 요소의 `children`배열에도 포함되지 않으며, `querySelector()`같은 일반적인 `DOM`순회 메서드에서 열거되지도 않는다.
