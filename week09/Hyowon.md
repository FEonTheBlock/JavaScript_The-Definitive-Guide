## 15.4 CSS 스크립트

### 15.4.1 CSS 클래스

- 자바스크립트로 문서 콘텐츠의 스타일을 바꾸는 가장 단순한 방법은 HTML 태그의 class 속성에 css 클래스를 추가하거나 제거하는 것
- classList 프로퍼티를 사용

### 15.4.2 인라인 스타일

- 문서에 툴팁 요소는 단 하나뿐이고，표시하기 전에 동적으로 위치를 바꾸는 경우
    - classList 프로퍼티가 별로 좋지 않음
    - style 속성을 설정
- 모든 Element 객체에는 style 속성에 대응하는 style 프로퍼티가 있다
- style 프로퍼티는 문자열이 아니라 CSSStyleDeclaration 객체
- 때로는 CSSStyleDeclaration 객체를 사용하는 것보다 요소의 인라인 스타일을 사용 하는게 쉬울 때도 있다
    - getAttribute() , setAttribute() 메서드를 사용하거나 CSSStyleDeclaration 객체의 cssText 프로퍼티를 사용하면 된다
        
        ```jsx
        f.setAttribute("style", e.getAttribute("style"));
        f.style.cssText = e.style.cssText;
        ```
        
- 요소의 style 프로퍼티를 가져올 때는 인라인 스타일만 가져올 수 있지만 스타일은 대부분 스타일시트로 지정된다는 점을 염두에 둬야함
- 또한 style 프로퍼티 에서 가져오는값은 단위나 단축프로퍼티 형식을 HTML속성에 있는그대로가져오므로 복잡한 파싱이 필요할 수도 있음
- 일반적으로 요소의 스타일을 가져올 때는 다음 절에서 설명할 계산된 스타일이 아마 적합할 것

### 15.4.3 계산된스타일

- 요소의 계산된 스타일은 브라우저가 요소의 인라인 스타일과 모든 스타일시트에서 가져온 적용 가능한 스타일 규칙 전체를 합해 계산한 프로퍼티 값 집합
- 요소를 표시할 때 실제로 시용되는 프로퍼티 집합
- 계산된 스타일 역시 CSSStyleDeclaration 객체로 표현
- 인라인 스타일과 달리 계산된 스타일은 읽기 전용
- Window 객체의 getComputedStyle() 메서드를 사용
- 첫 번째 인자는 계산된 스타일을 가져올 요소입니 다. 선택 사항인 두 번째 인자는 ::before, ::after 같은 CSS 가상 요소를 지정
    
    ```jsx
    let title = document.querySelector(“#sectionltitle");
    let styles = window.getComputedStyle(title);
    let beforeStyles = window.getComputedStyle(title, "::before");
    ```
    

- 인라인 스타일을 나타내는 CSS- StyleDeclaration 객체와 계산된 스타일을 나타내는 CSSStyleDeclaration 객체 사이 에는 몇 가지 중요한 차이가 있다
    - 계산된 스타일 프로퍼티는 읽기 전용
    - 계산된 스타일 프로퍼티는 절댓값
        - 퍼센트나 포인트 같은 상대 단위는 절댓값으로 변환
    - 단축 프로퍼티는 계산되지 않으며 베이스인 기본 프로퍼티만 계산됨
    - 계산된 스타일에는 cssText 프로퍼티가 존재하지 않음
    

### 15.4.4 스타일시트 스크립트

```jsx
function setTheme(name) {
	// <link rel="stylesheet">를 새로 만들어 스타일시트를 불러옵니다. 
	let link = document. createElement ("link");
	link.id "theme";
	link.rel = "stylesheet";
	link.href =`themes/${name}.css`;
	
	// id가 theme인 기존 링크를 찾습니다.
	let currentTheme = document.querySelector("#theme"); 
	if (currentTheme) {
	// 기존 테마가 있으면 새 테마로 교체합니다.
		currentTheme.replaceWith(link); 
	} 
	else {
	// 없다면 테마 스타일시트 링크를 삽입합니다. 
		document.head.append(link);
	}
}
```

- 트랜지션 이벤트
    - 이벤트의 핸들러에 전달되는 이벤트 객체는 트랜지션이벤트(TransitionEvent) 객체
    - 이 객체의 propertyName 프로퍼티는 애니메이션을 적용받는 css프로퍼티 이름
    - elapsedTime 프로퍼티는 transitionstart 이벤트로부터 경과되는 시간
- 애니메이션 이벤트
    - 핸들러 함수에 전달되는 이벤트 객체는 애니메이션이벤트(AnimationEvent) 객체
    

## 15.5 문서 지오메트리와 스크롤

- getBoundingClientRect() , elementFromPoint() 메서드와 마우스/포인터 이벤트 객체의 clientX, clientY 프로퍼티는 뷰포트좌표 사용
- 컨테이너 요소 내의 absolute 포지션이 지정된 요소의 위치를 기준으로 삼는 방식이 아주 유용할 때가 있다
    - 문서 좌표，뷰포트좌표와 구분하기 위해 이 새로운 좌표계를 ‘컨테이너 좌표’라고 부릅니다.
    

### 15.5.3 지정된 위치에 있는 요소 파악

- getBoundingClientRect() 메서드로 요소의 현재 위치를 알 수 있습니다.
- 반대로 뷰포트에서 특정 좌표에 있는 요소가 무엇인지 알아야 할 때도 있습니다. Document객체의 elementFromPoint() 메서드를사용하면 됩니다.
- 원하는x，y좌표 (예를 들어 마우스 이벤트의 clientX , clientY)로 이 메서드를 호출하면 지정된 위치에 있는 Element 객체가 반환됩니다.
    - 해당 지점에서 가장 안쪽의(가장 갚이 중첩된) 요소와 가장 위쪽의 (z-index 속성이 가장 큰) 요소를 반환 하는겁니다.

### 15.5.4 스크롤

- 원하는 요소가 보일 때까지 스크롤 하고 싶은 경우
    - 원하는 HTML요소에서 scrollIntoView() 메서드를 사용
    - 이 메서드는 호출된 요소가 뷰포트에 나타날 때까지 스크롤한다
    - 기본적으로 요소의 위쪽 경계를 뷰포트 위쪽 경계 에 맞추려 하지만， 인자로 false 하나만 전달하면 반대로 요소의 아래쪽 경계를 뷰포트의 아래쪽 경계에 맞출 수 있음
    - scrollIntoView()에 behavior:"smooth" 프로퍼티가 있는 객체를 전달해 부드러운 스크롤을 만들 수 있다.
    - block 프로퍼티를 써서 세로 위치를 정할 수 있고, 가로 스크롤이 필요하다면 inline 프로퍼티로 지정할 수 있습니다.
    

## 15.6 웹 컴포넌트

### 15.6.1 웹 컴포넌트 사용

- 웹 컴포넌트는 HTML 태그 이름을 직접 정의함
    - 중요한 것은 이 태그 이름에 반드시 하이픈이 들어가야 한다
    - HTML의 미래 버전에서 하이픈이 없는 새로운 태그를 도입하더라도 웹 컴포넌트와 충돌할 일은 없다.
- 웹 컴포넌트에는 스스로 닫는 태그를 쓸 수 없습니다.
- 일반적인 HTML 요소와 마찬가지로 웹 컴포넌트도 특정한 타입의 자식 요소를 받기도 하고， 반대로 자식 요소를 받지 않기도 합니다.
- ‘슬롯’에 특별한 라벨이 붙은 자식 요소를 선택적으로 받도록 작성되기도 합니다.
    
    ```jsx
    <search-box>
    	<img src="images/search-icon.png" slot="left" />
    	<img src="images/cancel-icon.png" slot="right" />
    </search-box>
    ```
    
- 웹 브라우저의 HTML 파서는 아주 유연하며 자신이 이해하지 못하는 요소도 관대하게 처리합니다.
- 브라우저는 컴포넌트가 정의되기 전에 웹 컴포넌트 태그를 만날 경우 범용 HTMLElement를 DOM 트리에 추가합니다. 그리고 나중에 커스텀 요소가 정의되면 그 범용 요소를 알맞게 ‘업그레이드’합니다.

### 15.6.2 HTML 템플릿

- 웹 브라우저는 <template> 태그와 그 자식 요소를 절대 렌더링하지 않으므로 <template> 태그는 자바스크립트에서만 사용할수 있습니다.
- 이 태그의 목적은 기본적인 HTML 구조가 웹 페이지에 여러 번 반복해야 할 때 <template>을 써서 한 번만 정의하고， 필요한 만큼 자바스크립트로 복사해서 쓰는 것입니다.
- 자바스크립트에서 <template> 태그는 HTMLTemplateElement 객체로 나타낸다. 이 객체에는 content 프로퍼티 단 하나만 존재함

### 15.6.4 섀도우 DOM

- 섀도우 DOM 캡슐화
    - 섀도우 DOM의 핵심 특정은 캡슐화입니다.
    - 섀도우 루트의 자손 요소는 일반적인 DOM 트리에 독립적입니다.
    - 아예 다른문서에 존재한다고해도 과언이 아닐정도로
    - 섀도우 DOM에 들어 있는 요소는 querySelectorAll() 같은 일반 적인 DOM 메서드에 노출되지 않습니다. 섀도우 루트를 생성할 때 ‘열린’모드와 ‘닫힌’ 모드를 선택할 수 있습니다. 닫힌 새도우 루트는 완전히 밀봉되며 접근도 불가능합니다. 하지만 섀도우 루트는 대부분 ‘열린’ 모드로 생성되며 섀도우 호스트에 shadowRoot 프로퍼티가 생기므로 필요하다면 자바스크립트로 섀도우 루트의 요소에 접근할 수 있습니다
    - 섀도우 루트 아래에서 정의한 스타일은 해당 트리에 종속되며 외부에 있는 라이트 DOM 요소에는 절대 영향을 끼치지 않습니다.
    - 섀도우 DOM 안에서 일어나는 load 같은 일부 이벤트는 섀도우 DOM으로 제한 됩니다. 반면，포커스， 마우스， 키보드 이벤트 같은 이벤트에는 버블링이 적용됩 니다. 섀도우 DOM에서 일어난 이벤트가 경계를 넘어 라이트 DOM으로 전달되기 시작하면 target 프로퍼티가 섀도우 호스트 요소로 변경되므로 그 요소에서 직접 발생한것처럼 보입니다.
