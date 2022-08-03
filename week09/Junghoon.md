# CSS 스크립트

자바스크립트 코드로 CSS를 다루는 방법

- CSS 클래스
- 인라인 스타일
- 계산된 스타일
- 스타일시트 스크립트
- CSS 애니메이션과 이벤트

# 웹 컴포넌트

**재사용** 가능한 커스텀 엘리먼트를 생성하고 웹 앱에서 활용할 수 있도록 해주는 다양한 기술들의 모음  
세 가지 주요 기술들로 구성되며, 재사용을 원하는 어느 곳이든 코드 충돌에 대한 걱정이 없는 캡슐화된 기능을 갖춘 다용도의 커스텀 엘리먼트를 생성하기 위해 함께 사용될 수 있다.

1. customElements  
   새로운 HTML 요소를 만들거나 기존 HTML 요소를 확장할 수 있다.
2. Shadow DOM  
   문서의 다른 부분과 충돌할 염려 없이 스크립트를 작성하고 스타일을 지정할 수 있도록 캡슐화된 Shadow DOM 트리를 요소에 첨부하기 위한 API 모음
3. HTML Template  
   렌더링된 페이지에 표시되지 않는 마크업 템플릿을 작성하여 customElements 구조의 기초로 여러 번 재사용 할 수 있다.

**customElements.define()**

- 페이지에 커스텀 엘리먼트를 등록하는 메서드
- 첫 번째 인자로 html에서 사용할 이름을, 두 번째 인자로 엘리먼트의 행위가 정의된 클래스를 전달한다
- 이 메서드를 실행하는 함수를 모듈로 내보내 DOMContentLoaded 이벤트가 실행되면 defineMainHeader를 실행하도록 했다

**lifecycle callback**  
커스텀 엘리먼트의 클래스 내에서 라이프사이클 콜백을 지정할 수 있다

- **connectedCallback** - 사용자 정의 요소가 문서의 DOM에 처음 연결될 때 호출
- **disconnectedCallback** - 사용자 정의 요소가 문서의 DOM에서 연결 해제될 때 호출
- **adoptedCallback** - 사용자 정의 요소가 새 문서로 이동할 때 호출
- **attributeChangedCallback** - 사용자 정의 요소의 어트리뷰트 중 하나가 추가, 제거 또는 변경될 때 호출

**observedAttributes**

- attributeChangedCallback 함수를 실행시키려면 observedAttributes에 관찰할 어트리뷰트를 명시해야 한다.
- observedAttributes에 명시한 어트리뷰트 값이 변경되면 attributeChangedCallback를 실행한다.

## Shadow DOM

Shadow DOM은 결코 새로운 것이 아니다. 흔히들 알고 있는 `<video>` 태그나 `<input type=“range” />`는 Shadow DOM 내부에 요소들이 포함되어 있다.  
Shadow DOM을 사용하면 숨겨진 DOM 트리를 일반 DOM 트리의 요소에 첨부할 수 있다.  
Shadow DOM 트리는 모든 요소에 첨부할 수 있는 shadow 루트로 시작한다.

- Shadow host - shadow DOM이 연결된 일반 DOM 노드
- Shadow tree - shadow DOM 내부의 DOM 트리
- Shadow boundary - shadow DOM이 끝나고, DOM이 시작되는 곳
- Shadow root - shadow tree의 루트 노드

**특징**

- 노드와 같은 방식으로 Shadow DOM 노드에 영향을 줄 수 있다.
- 차이점은 Shadow DOM 내부의 어떤 코드도 외부에 영향을 줄 수 없으므로 캡슐화를 허용한다는 것
- 이러한 특징을 이용하여 Web Component의 완성도를 더 높일 수 있다.

**Element.attachShadow()**

- Shadow root를 연결하는 메서드
