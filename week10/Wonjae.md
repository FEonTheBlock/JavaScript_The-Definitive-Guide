# 15 | 웹 브라우저의 자바스크립트 (part3)

## SVG(Scalable Vector Graphics, 확장성 있는 벡터 그래픽)

> SVG는 이미지 형식이다.
> <br>이름에 들어있는 `벡터`라는 단어는 이 이미지가 픽셀 값 행렬로 이뤄진 `GIF`, `JPEG`, `PNG` 같은 비트맵 이미지와는 다름을 암시한다.
> <br>SVG 이미지는 원하는 그래픽을 표한하는데 필요한 단계들을 해상도와 상관없이 정밀하게표현하는 일종의 언어이다.

### 웹 브라우저에서 SVG를 사용하는 방법

1. .png나 .jpeg 이미지와 마찬가지로 `<img>`태그 안에 .svg 이미지 파일을 사용할 수 있다.
2. SVG는 XML 기반이며 HTML과 비슷한 형식이므로 HTML문서에 SVG태그를 직접 사용할 수 있다.
   - 이렇게하면 XML 네임스페이스를 생략하더라도 브라우저의 HTML 파서가 SVG 태그를 HTML 태그처럼 취급한다.
3. 필요에 따라 DOM API를 사용해 동적으로 SVG 요소를 생성해 이미지를 만들 수 있다.

### HTML 속 SVG

> SVG 이미지는 HTML `<img>`태그로 표시할 수 있다.

```html
<html>
  <head>
    <title>Analog Clock</title>
    <style>
      /* 이 C55 스타일은 아래에 정의한 5VG 요소에 적용될니다. */
      /* 시계에 적용할 스타일 */
      #clock {
        stroke: black; /* 검은색 직선 */
        stroke-linecap: round; /* 선 끝을 둥글게 마감합니다. */
        fill: #ffe; /* 배경은 흰색에 가깝습니다. */
      }
      /* 시계 외곽선 */
      #clock .face {
        stroke-width: 3;
      }
      /* 매시를 나타내는 짧은 선 */
      #clock .ticks {
        stroke-width: 2;
      }
      /* 시칩과 분침 */
      #clock .hands {
        stroke-width: 3;
      }
      /* 숫자 스타일 */
      #clock .numbers {
        font-family: sans-serif;
        font-size: 10;
        font-weight: bold;
        text-anchor: middle;
        stroke: none;
        fill: black;
      }
    </style>
  </head>
  <body>
    <svg id="clock" viewBox="0 0 100 100" width="2S0" height="2S0">
      <circle class="face" cx="50" cy="50" r="45" />
      <!-- 시계 전체 -->
      <g class="ticks">
        <!-- 12시간 기준， 127H의 짧은 선 -->
        <line x1="50" y1="5.000" x2="50.00" y2="10.00" />
        <line x1="72.50" y1="11.03" x2="70.00" y2="15.36" />
        <line x1="88.97" y1="27.50" x2="84.64" y2="30.00" />
        <line x1="95.00" y1="50.00" x2="90.00" y2="50.00" />
        <line x1="88.97" y1="72.50" x2="84.64" y2="70.00" />
        <line x1="72.50" y1="88.97" x2="70.00" y2="84.64" />
        <line x1="50.00" y1="95.00" x2="50.00" y2="90.00" />
        <line x1="27.50" y1="88.97" x2="30.00" y2="84.64" />
        <line x1="11.03" y1="72.50" x2="l5.36" y2="70.00" />
        <line x1="5.000" y1="50.00" x2="10.00" y2="50.00" />
        <line x1="11.03" y1="27.50" x2="15.36" y2="30.00" />
        <line x1="27.50" y1="ll.03" x2="30.00" y2="15.36" />
      </g>
      <g class="numbers">
        <!-- 시계 g항으로 12AI, 3AI, 6AI, 9시 -->
        <text x="50" y="18">12</text>
        <text x="85" y="53">3</text>
        <text x="50" y="88">6</text>
        <text x="15" y="53">9</text>
      </g>
      <g class="hands">
        <!-- 시칩과 분칩을 수직 방향으로 표시합니다. -->
        <line class="hourhand" x1="50" y1="50" x2="50" y2="25" />
        <line class="minutehand" x1="50" y1="50" x2="50" y2="20" />
      </g>
    </svg>
    <script src="clock.js"></script>
  </body>
</html>
```

### SVG 스크립트

> `<img>`태그를 사용하지않고 HTML파일에 SVG를 직접 넣는건 DOM API를 통해 SVG 이미지를 조작할 수 있기 때문이기도 하다.

- ex) 시침, 분침, 초침 회전

### JS로 SVG 이미지 생성

> 스크립트로 HTML 문서에 포함된 SVG이미지를 조작할수도 있지만 처음부터 만드는 것도 가능하다.

- HTML 문서에 SVG 태그를 쓸 수 있긴 하지만, SVG 태그는 엄밀히 말해 HTML태그가 아니라 XML 태그이며 `createElement` 함수로는 SVG요소를 만들 수 없다.
- 이를 만들려면 반드시 XML 네임스페이스 문자열을 첫 번째 인자로 받는 `createElementNS()`를 사용해야 한다.
- SVG 네임스페이스는 리터럴 문자열 `http://www.w3.org/2000/svg`이다.

## `<canvas>`의 그래픽

> `<canvas>`요소는 그 자체로는 아무것도 표현하지 않지만 일종의 도화지를 만들고 클라이언트 사이드 JS에서 사용할 수 있는 강력한 API를 제공한다.

- 캔버스 API와 SVG의 가장 큰 차이는 캔버스는 메서드를 호출하는 방식으로 그림을 그리는 반면, SVG는 XML 요소의 트리를 만드는 방식으로 그림을 그린다는 것이다.
- 두 방법 모두 강력하며 기능에는 큰 차이가 없다.
- 하지만 표면적으로 볼때는 상당히 다르고 각 방법에 장단점이 존재한다.
- 예를 들어 SVG 그래픽은 요소를 삭제하는 방식으로 쉽게 수정할 수 있지만 `<canvas>`는 같은 그래픽의 요소 하나를 제거하더라도 처음부터 다시 그려야할때가 많다.

### 캔버스와 3차원 그래픽

> 문자열 webgl로 getContext()를 호출하면 WebGL API를 써서 3차원 그래픽을 그릴 수 있는 컨텍스트 객체를 얻는다.
> <br>WebGL은 방대하고 복잡하며, JS 프로그래머가 GPU에 접근해 커스텀 셰이더를 작성하거나 아주 강력한 그래픽 동작을 사용할 수 있게 허용하는 저수준 API이다.

```html
<p>
  This is a red square: <canvas id="square" width="10" height="10"></canvas>.
</p>
<p>
  This is a blue circle: <canvas id="circle" width="10" height="10"></canvas>.
  <script>
    let canvas = document.querySelector('#square'); // 첫 번째 캔버스 요소
    let context = canvas.getContext('2d'); // 2차원 컨텍스트
    context.fillStyle = '#f00'; // 채울 색깔을 빨간색으로 정합니다.
    context.fillRect(0, 0, 10, 10); // 사각형을 채옵니다.
    canvas = document.querySelector('#circle'); // 두 번째 캔버스 요소
    context = canvas.getContext('2d'); // 컨텍스트를 가져옵니다 .
    context.beginPath(); // 새로운 패스
    context.arc(5, 5, 5, 0, 2 * Math.PI, true); // 패스에 원을 추가합니다.
    context.fillStyle = '#00f'; // 채울 색깔을 파란색으로 정합니다.
    context.fill(); // 패스를 채읍니다.
  </script>
</p>
```

- SVG에서는 복잡한 도형을 그리거나 채울 수 있는 직선과 곡선을 모은 패스라고 부른다.
- 캔버스 API 역시 패스라는 용어를 사용한다.
- 캔버스는 패스를 문자열로 만들지 않고 이전 예제의 `beginPath()`, `arc()`같은 메서드를 이어서 만든다.

### 패스와 다각형

> 캔버스에 직선을 그리고 그 직선들로 둘러싸인 영역을 채울때는 먼저 패스를 정의한다.
> <br>패스는 하나 이상의 서브패스의 연속이다.
> <br>서브패스는 직선 또는 곡선으로 이어진 두개 이상의 지점의 연속이다.
> <br> 새로운 패스를 시작할 때는 `beginPath()` 메서드를 사용한다.
> <br> 새로운 서브패스는 `moveTo()`메서드로 시작한다.

### 코흐 눈송이 예제

```js
let deg = Math.PI / 180;

/**
 * @param {*} ctx: context
 * @param {*} n: 코흐 눈송이 프랙탈 레벨
 * @param {*} x: 좌측 하단 모서리 x
 * @param {*} y: 좌측 하단 모서리 y
 * @param {*} len: 변의 길이
 */
function snowflake(ctx, n, x, y, len) {
  ctx.save();
  ctx.translate(x, y);
  ctx.moveTo(0, 0);
  leg(n);
  ctx.rotate(-120 * deg);
  leg(n);
  ctx.rotate(-120 * deg);
  leg(n);
  ctx.closePath();
  ctx.restore();

  function leg(n) {
    ctx.save();
    if (n === 0) {
      ctx.lineTo(len, 0);
    } else {
      ctx.scale(1 / 3, 1 / 3);
      leg(n - 1);
      ctx.rotate(60 * deg);
      leg(n - 1);
      ctx.rotate(-120 * deg);
      leg(n - 1);
      ctx.rotate(60 * deg);
      leg(n - 1);
    }
    ctx.restore();
    ctx.translate(len, 0);
  }
}

let ctx = document.querySelector('canvas').getContext('2d');
snowflake(ctx, 0, 25, 125, 125);
snowflake(ctx, 1, 175, 125, 125);
snowflake(ctx, 2, 325, 125, 125);
snowflake(ctx, 3, 475, 125, 125);
snowflake(ctx, 4, 625, 125, 125);
ctx.stroke();
```

## 오디오 API

> HTML `<audio>`, `<video>`태그를 통해 웹 페이지에 사운드와 비디오를 넣을 수 있다.
> <br>이들의 API는 상당히 복잡하며 사용자 인터페이스도 결코 간단하지 않다.

- `play()`, `pause()`: 미디어 재생 제어
- `volumn`, `play`, `backRate`: 오디오 볼륨과 재생 속도를 제어하는 프로퍼티
- `currentTime`: 원하는 재생 위치를 설정 가능한 프로퍼티

### Audio() 생성자

> HTML문서에 `<audio>`태그를 삽입하지 않아도 웹 페이지에서 사운드 효과를 이용할 수 있다.
> <br>DOM의 `document.createElement()` 메서드로 `<audio>` 요소를 동적으로 생성하거나 그냥 `Audio()` 생성자를 써도 된다.

```js
let soundEffect = new Audio('soundEffect.mp3');

document.addEventListener('click', () => {
  soundEffect.cloneNode().play();
});
```

- 위 예제에서는 `cloneNode()`를 사용했는데, 그 이유는 사용자가 마우스를 빠르게 클릭했을 때 각 클릭에 대응할 audio 요소가 여러 개 필요할 수 있기 때문이다.
- 복사한 `audio` 요소는 문서에 추가하지 않았으므로 재생을 마친 후 가비지 컬렉션 대상이 된다.

### WebAudio API

> 웹 브라우저는 `audio`요소를 통해 녹음된 사운드를 재생하기도 하지만, `WebAudio API`를 통해 사운드를 직접 생성해서 재생할 수도 있다.

## 위치, 내비게이션, 히스토리

> `window`와 `document`객체의 `location` 프로퍼티는, 현재 창에 표시되는 문서의 `URL`을 나타내고 창에 새로운 문서를 불러오는 `API`를 제공하는 `location`객체를 참조한다.

- `location`객체는 `protocol`, `hostname`, `port`, `path`같은 프로퍼티를 사용해 현재 문서의 `URL`의 다양한 부분에 접근할 수 있다.

### 새로운 문서 로딩

> `location`객체의 프로퍼티는 쓰기 가능이며 이들의 값을 바꾸면 `URL`이 바뀌고 브라우저가 새로운 문서를 불러온다.

```js
document.location.path = 'pages/3.html'; // 새로운 페이지 로드
document.location.hash = 'TOC'; // 차례 위치까지 스크롤
document.location.search = '?page=' + (page + 1); // 새로운 쿼리스트링으로 다시 로드
```

### pushState()를 사용한 히스토리 관리

**`pushState()`인자**

1. 문서의 현재 상태를 복원하는데 필요한 상태 정보를 포함한 객체
2. 상태의 타이틀 구실을 할 문자열(대부분의 브라우저에서 지원하지 않으므로 빈 문자열을 전달해야 함)
3. (`option`): 사용자가 뒤로 가기/앞으로 가기 버튼을 눌렀을 때 주소 표시줄에 표시될 URL이다.
   - 상대 URL은 문서의 현재 위치를 기준으로 해석된다.

- 각 상태와 URL을 연결하면 사용자가 애플리케이션의 내부 상태를 북마크할 수 있다.
- 하지만 사용자가 나중에 북마크를 통해 돌아온다면 `popstate` 이벤트가 일어나지 않으므로 URL을 분석해서 애플리케이션 상태를 복원해야 한다.
