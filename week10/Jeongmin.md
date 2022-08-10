# 정민 10주차 학습(15.10까지)

## SVG

SVG는 Scalable Vector Graphics 라는 용어의 약자로, 이름에 들어 있는 '벡터'라는 단어는 이 이미지가 픽셀 값 행렬로 이뤄진 비트맵 이미지와는 다름을 암시한다. SVG '이미지'는 원하는 그래픽을 표현하는 데 필요한 단계들을 해상도와 상관없이(따라서 확대/축소가 자유롭다) 정밀하게 표현하는 일종의 언어이다.

> SVG 이미지는 XML 마크업 언어를 사용해 텍스트 파일로 작성한다.

웹 브라우저에서 SVG를 사용하는 방법 3가지

1. `<img>` 태그 안에 `.svg` 이미지 파일을 사용
2. HTML 문서에 SVG 태그를 직접 사용
3. 필요에 따라 DOM API를 사용해 동적으로 SVG 요소를 생성

### HTML 속 SVG

svg 태그의 자손 요소는 일반적인 HTML 태그가 아니며, 안에 `<circle>, <line>, <text>`등의 태그를 가지고 있다. 또한 이를 꾸미는데 사용되는 `fill, stroke-width, text-anchor` 같은 스타일은 일반적인 CSS 스타일 프로퍼티가 아니다. SVG 태그에서는 font 단축 프로퍼티가 동작하지 않으므로 `font-family, font-size, font-weight`를 따로따로 써야한다.

### SVG 스크립트

`<img>` 태그를 사용하지 않고 HTML 파일에 SVG를 직접 넣는 건 DOM API를 통해 SVG를 조작할 수 있기 때문이다. 일반적인 DOM 조작과 비슷하게, SVG의 요소를 DOM 조작하여 속성을 동적으로 바꾸어 SVG를 조작할 수 있다.

> `document.querySelector` API를 이용하여 DOM 요소를 가져와서 `setAttribute` 같은 것으로 조작한다.

### 자바스크립트로 SVG 이미지 생성

SVG 태그는 엄밀히 말해 HTML 태그가 아니라 XML 태그이기 때문에 `createElementNS()`를 사용하여 만들수 있다. 이 함수는 XML 네임스페이스 문자열(`http://www.w3.org/2000/svg`)을 첫 번째 인자로 받는다.

책의 예제에서는 실제 파이 조각을 그리는 `path` 부분까지 코드가 나와있는데, 너무 자세하기 때문에 간단한 코드만 보자.

```js
const svg = "http://www.w3.org/2000/svg";

// svg 태그 생성
const chart = document.createElementNS(svg, "svg");
chart.setAttribute("width", width);
chart.setAttribute("height", height);
chart.setAttribute("viewBox", `0 0 ${width} ${height}`);
```

> 이후 뒷부분에서 각도를 조절하고, 수학적 계산을 통해 `path`의 속성값과 데이터 키에 사용될 여러가지 태그를 만들어 svg를 동적으로 사용하는 예시를 다룬다.

## `<canvas>`의 그래픽

캔버스 API와 SVG의 가장 큰 차이는 캔버스는 메서드를 호출하는 방식으로 그림을 그리는 반면, SVG는 XML 요소의 트리를 만드는 방식으로 그림을 그린다는 것이다. 둘다 장단점이 있는데 SVG 그래픽은 요소를 삭제하는 방식으로 쉽게 수정할 수 있지만 `<canvas>`에서 같은 그래픽의 요소 하나를 제거하더라도 처음부터 다시 그려야 할 때가 많다.

> 그러나 캔버스 API는 JS 기반이라 SVG 문법에 비해 비교적 간결하다.

캔버스 API는 `getContext()` 메서드를 호출해 얻는 컨텍스트 객체에 존재한다. 인자 2d를 넘겨서 `getContext()`를 호출하면 2차원 그래픽을 그릴 수 있는 `CanvasRenderingContext2D` 객체를 얻는다.

`간단한 canvas 예제`

```js
let canvas = document.querySelector("#square");
let context = canvas.getContext("2d");
context.fillStyle = "#f00";
context.fillRect(0, 0, 10, 10);

canvas = document.querySelector("#circle");
context = canvas.getContext("2d");
context.beginPath();
context.arc(5, 5, 5, 0, 2 * Math.PI, true);
context.fillStyle = "#00f";
context.fill();
```

캔버스는 SVG와 달리 패스를 문자열로 만들지 않고 이전 예제의 `beginPath(), arc()` 같은 메서드를 이어서 만든다. 이후 패스를 완성한뒤, `fill()` 같은 다른 메서드를 적용한다.

### 패스와 다각형

패스는 하나 이상의 서브패스의 연속으로, 새로운 패스를 시작할 때 `beginPath()` 메서드를 사용한다. 이후 `moveTo()` 메서드로 시작할 지점을 지정하고 `lineTo()` 메서드를 이용하여 직선을 그을 수 있다. 그러나 이 직선들은 아무것도 그리지 않고 `stroke()` 메서드나 `fill()`을 사용하여 화면에 그릴 수 있다.

일반적으로 서브패스는 '열려'있기 때문에 모두 그리고 난 후에는 `closePath()` 메서드를 호출하여 닫아주어야 한다.

캔버스에서 서브패스가 겹치거나 교차할 때 어떤 영역이 내부에 있고 어떤 영역이 외부에 있는지 판단해야 한다. 캔버스는 '넌제로 와인딩 규칙'을 이용해 이를 판단한다.

> 육각형 안의 사각형이 있는 예제에서는 육각형과 사각형의 꼭짓점들이 서로 반대 방향으로 그러졌기에 이 규칙이 적용된다.

[위키백과 - Nonzero Winding Rule](https://ko.wikipedia.org/wiki/Nonzero_Winding_Rule)

### 캔버스의 크기와 좌표

캔버스의 width, height 속성은 캔버스가 실제로 사용할 픽셀 숫자이다. 예를들어 각각 100이면 캔버스는 10,000 픽셀을 표현한다.

> 하지만 이러한 width, height 속성으로 캔버스의 화면 크기를 지정하지 말고, 대신 CSS의 width, height 스타일을 사용하여 `window.devicePixelRatio`를 곱한 값으로 설정해야 메모리 픽셀과 물리적 픽셀이 대응되게 할 수 있다.

**그래픽 상태 저장과 복원**

`<canvas>` 요소에는 컨텍스트 객체가 오직 하나만 존재하며, `getContext()`를 여러 번 호출하더라도 같은 `CanvasRendering-Context2D` 객체가 반환된다. 캔버스 API가 한 번에 한 가지 그래픽 속성 세트만 허용하긴 하지만, 현재 그래픽 사앹를 저장할 수 있으므로 수정했다가 쉽게 복원할 수 있다. (`save()` 메서드와 `restore()` 메서드 사용)

## 오디오 API

HTML `<audio>`, `<video>` 태그를 통해 웹 페이지에 사운드와 비디오를 넣을 수 있다.

### Audio() 생성자

HTML 문서에 `<audio>` 태그를 삽입하지 않아도 웹 페이지에서 사운드 효과를 이용할 수 있다. `<audio>` 요소를 생성하여 문서에 추가할 필요없이 `play()` 메서드를 호출하면 된다.

`예시`

```js
// 사용할 사운드 효과를 미리 불러온다.
const soundeffect = new Audio("soundeffect.mp3");

// 사용자가 마우스 버튼을 클릭할 때마다 사운드를 재생한다.
document.addEventListener("click", () => {
  soundeffect.cloneNode().play(); // 사운드를 불러와서 재생
});
```

> audio 요소는 문서에 추가하지 않았으므로 재생을 마친 후 가비지 컬렉션 대상이 된다.

### WebAudio API

이 API를 통해 파형의 소스, 변환, 대상을 나타내는 AudioNode 객체를 만들고 이 노드를 한데 묶어서 사운드를 만들 수 있다.

## 위치, 내비게이션, 히스토리

Window와 Document 객체의 location 프로퍼티는, 현재 창에 표시되는 문서의 URL을 나타내고 창에 새로운 문서를 불러오는 API를 제공하는 Location 객체를 참조한다.

이후 뒷 절에서는 내부 프로퍼티와 관련한 이야기를 다루며 히스토리와 관련한 `hashChange`, `pushState()` 등을 소개한다. 이와 관련하여 참고하면 좋은 자료가 있어 첨부.

[SPA & Routing](https://poiemaweb.com/js-spa)
