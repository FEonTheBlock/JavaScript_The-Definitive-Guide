## 15.11 네트워크

### 15.11.1 fetch()

- 기본적인 HTTP요청에서 fetch()는3단계로 동작
    1. 콘텐츠를 가져올 URL을 전달하면서 fetch()를 호출
    2. HTTP 응답이 도착하기 시작하면 1단계에서 비동기적으로 반환한 응답객체를
        
        가져오고 이 응답 객체의 메서드를 호출해 응답 바디를 가져옴
        
    3. 2단계에서 비동기적으로 반환한 바디 객체를 사용해 필요한 일을 합니다.
- fetch() API는 완전히 프라미스 기반이고 비동기 단계가 두 단계 있으므로 fetch()를 사용할 때는 일반적으로 then()을 두 번 호출하거나 await 표현식을 두 번 쓴다.
- fetch()가 반환하는 프라미스는 응답객체로 해석된다.
    - 이 객체의 status 프로퍼티는 HTTP 상태 코드
    - statusText 프로퍼티에는 각 상태 코드에 대응하는 영문 텍스트가 저장
    - ok 프로퍼티는 status가 200-299 사이의 숫자일 때 true이고 다른 숫자일 때 false
- fetch()는 서버의 응답이 도착하기 시작할 때, 즉  HTTP 상태와 응답 헤더를 받는 즉시 프라미스를 해석 (resolve)한다. 일반적으로 이 시점은 응답 바디 전체가 도착하기 전이다.
    - 응답 객체의 headers 프로퍼티는 Headers 객체
- 웹 서버가 fetch() 요청에 응답한다면 프라미스는 그 응답 객체로 이행(fulfill)됨
    - 서버의 응답이 404(찾을수없음)또는 500(서버 에러)이더라도 마찬가지이므로 항상 .catch()를 함께 사용
- URL과 함께 매개변수를 전달해야 한다면 URL 마지막 ? 뒤에 이름-값 쌍을 추가
- 11.9절에서 설명한 URL과 URLSearchParams 클래스 를 사용하면 URL을 이런 형태로 만들기 쉽고， fetch() 함수는 첫 번째 인자로 URL 객체도 받으므로 다음과 같이 fetch() 요청에 요청 매개변수를 넣을 수 있습니다.
    
    ```jsx
    async function search(term) {
    	let url = new URL("/api/search");
    	url.searchParams.set("q" , term);
    	let response = await fetch(url);
    	if (!response.ok) throw new Error(response.statusText); 
    	let resultsArray = await response.json();
    	return resultsArray;
    }
    ```
    
- fetch() 요청을 보내면서 헤더를 설정해야 할 때 두 번째 인자 사용
