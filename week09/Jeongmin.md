# 정민 9주차 학습(15.6까지)

## CSS 스크립트

이 장에서는 JS 코드로 CSS를 다루는 방법을 설명한다.

### CSS 클래스

JS로 문서 콘텐츠의 스타일을 바꾸는 가장 단순한 방법은 HTML 태그의 class 속성에 CSS 클래스를 추가하거나 제거하는 것

→ `classList` 프로퍼티를 사용하여 `add` 또는 `remove`를 하면 쉽다.

### 인라인 스타일

클래스 스타일을 미리 만들어 두기 어렵다면 아래와 같이 인라인 스타일을 사용하는 방법이 있다.

```js
function displayAt(tooltip, x, y) {
  tooltip.style.display = "block";
  tooltip.style.position = "absolute";
  tooltip.style.left = `${x}px`;
  tooltip.style.top = `${y}px`;
}
```

> 인라인 스타일은 `style` 프로퍼티를 이용하는 방법이며 `style` 프로퍼티는 문자열이 아닌 **CSSStyleDeclaration 객체**이다. 이 객체는 `style` 속성에 텍스트 형태로 존재하는 CSS 스타일을 파싱한 결과이다.

위에서 보았듯이, `CSSStyleDeclaration` 객체의 스타일 프로퍼티를 사용할 때는 값을 반드시 문자열로 바꾸어야한다.

`CSSStyleDeclaration` 객체를 사용하는 것보다 요소의 인라인 스타일을 사용하는 게 쉬울 때도 있다. 이런 경우, `getAttribute()`, `setAttribute()` 메서드를 사용하거나 `CSSStyleDeclaration` 객체의 `cssText` 프로퍼티를 사용하자.

```js
// 요소 e의 인라인 스타일을 요소 f에 복사
f.setAttribute("style", e.getAttribute("style"));

// 위의 코드 결과와 동일한 cssText 사용예시
f.style.cssText = e.style.cssText;
```

### 계산된 스타일

계산된 스타일을 구할 때는 Window 객체의 `getComputedStyle()` 메서드를 사용하자.

> 첫 번째 인자는 계산된 스타일을 가져올 요소, 두 번째 인자는 선택사항으로 `::before`, `::after` 같은 CSS 가상 요소이다.

`getComputedStyle()`의 반환값은 지정된 요소에 적용된 스타일 전체를 나타내는 `CSSStyleDeclaration` 객체이다. 이는 인라인 스타일의 `CSSStyleDeclaration` 객체와 차이가 있다.

- 읽기 전용이다.
- 계산된 스타일 프로퍼티는 절댓값이다.(크기를 나타내는 프로퍼티는 모두 픽셀 값으로 바뀐다.)
- 단축 프로퍼티는 계산되지 않고 베이스인 기본 프로퍼티만 계산된다.
- 계산된 스타일에는 `cssText`가 존재하지 않는다.

CSS로 요소의 위치와 크기를 정확히 지정할 수 있지만 계산된 스타일을 통해 요소의 위치와 크기를 가져오는 것은 권하지 않는다.

> 15.5.2에 나오는 요소의 위치 검색 참고(`getBoundingClientRect()`)

### 스타일시트 스크립트

JS로 스타일시트 자체도 조작할 수 있다. `<style>`, `<link>` 태그의 Element 객체에는 `disabled` 프로퍼티가 있으며 이를 통해 스타일시트 전체를 비활성화할 수 있다. 마찬가지로 DOM 조작 방법을 통해 새로운 스타일시트를 삽입할 수도 있다.

> `disabled`를 사용해 비활성화가 가능하다는 점과 JS의 `append` 또는 `replaceWith`와 같은 메서드로 스타일시트를 추가 및 교체가 가능하다는 것을 알아두면 될듯

### CSS 애니메이션과 이벤트

- `transitionrun`: 트랜지션이 처음 시작하면 이 이벤트 발생. `transition-delay`와 같은 시각적인 변화가 시작되기 전 이벤트가 먼저 일어날 때를 말함
- `transitionstart`: 시각적인 변화가 일어남과 동시에 이 이벤트 발생
- `transitionend`: 애니메이션 완료되면 이 이벤트 발생

트랜지션과 마찬가지로 애니메이션 역시 JS 코드에서 주시할 수 있는 이벤트를 일으킨다.

- `animationstart`: 애니메이션이 시작할 때
- `animationend`: 애니메이션이 끝날 때
- `animationiteration`: 애니메이션이 두 번 이상 반복되면 마지막을 제외하고 반복마다 이벤트 발생

## 문서 지오메트리와 스크롤

DOM 요소의 위치를 정확히 파악하기 위한 개념들에 대해 알아보자.

### 문서 좌표와 뷰포트 좌표

뷰포트(창기준): 문서 콘텐츠를 실제로 표시하는 부분으로 메뉴와 툴바, 탭 등을 제거한 부분이다.

> 요소의 위치를 언급할 땐 반드시 문서 좌표인지 뷰포트 좌표인지를 확실히 해야한다.

문서가 뷰포트보다 작거나 스크롤 되지 않았다면 문서 좌표계와 뷰포트 좌표계가 일치한다. 그러나 일반적으로 두 좌표계를 변환할 때는 반드시 스크롤 오프셋을 더하거나 빼야한다.

![](https://velog.velcdn.com/images/hustle-dev/post/8482be54-ef24-42be-b968-12b45b5b232d/image.png)

참고: https://ko.javascript.info/coordinates

> 위에서 보는 pageY, pageX가 문서좌표이고 clientY, clientX가 뷰포트 좌표이다.

일반적으로 문서 좌표엔 큰 의미가 없어서 뷰포트 좌표를 사용하는 경우가 많다.

뷰포트 좌표 관련 메서드

- `getBoundingClientRect()`
- `elementFromPoint()`
- 마우스/포인터 이벤트 객체의 `clientX`, `clientY`

### 요소의 위치 검색

`getBoundingClientRect()` 메서드를 호출하면 `left`, `right`, `top`, `bottom`, `width`, `height` 프로퍼티가 있는 객체를 반환한다.

> 이 메서드를 이용하여 위치를 파악할 수 있음

### 지정된 위치에 있는 요소 파악

`getBoundingClientRect()` 메서드와 달리 특정 좌표에 있는 요소가 무엇인지 알기위해 `elementFromPoint()` 메서드를 이용할 수 있다.

> 해당 지점에서 가장 안쪽의 요소와 가장 위쪽의 요소를 반환한다.

### 뷰포트 크기, 콘텐츠 크기, 스크롤 위치

이 부분은 그림을 보면 쉽게 이해할 수 있을 것 같아 그림을 첨부한다.

`clientWidth & clientHeight`

![](https://velog.velcdn.com/images/hustle-dev/post/3970fda4-556e-4b95-a2e8-a324db2eefe7/image.png)

`offsetWidth & offsetHeight`

![](https://velog.velcdn.com/images/hustle-dev/post/bb8722c4-e611-4203-9edd-c4add18fe59c/image.png)

`scrollWidth & scrollHeight와 전체 비교`

![](https://velog.velcdn.com/images/hustle-dev/post/0e9ca5a8-c3c6-47e0-97d6-a7bd84cee7c3/image.png)

> Element 객체의 다른 프로퍼티와 달리 `scrollLeft`, `scrollTop`은 쓰기 가능한 프로퍼티이며 이 값을 설정해 요소 안에서 콘텐츠를 스크롤할 수 있다.

## 웹 컴포넌트

책 안의 내용도 있지만 저번에 따로 정리했던 글이 있어서 같이 첨부.

[웹 컴포넌트를 알아보자](https://velog.io/@hustle-dev/%EC%9B%B9-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)
