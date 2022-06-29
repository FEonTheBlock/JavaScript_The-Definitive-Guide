
|  | 정보 |
|---|---|
| date | 2022-06-29 |
| group | FEOnTheBlock |
| link | [[완벽가이드] ...](#) |


> 웹 브라우저의 자바스크립트 프로그램은 일반적으로 이벤트 주도적이다. 따라서 서버는 반적으로 네트워크를 통해 클라이언트 요청이 들어온 후에야 작업을 시작한다. 
> 
> 이번 장에서 배울 것:
> 1. 비동기 코드를 쉽게 만드는 중요한 기능:  promise, async, await
> 2. 단순한 루프에서 비동기 이벤트 스트림 다루기: ES2018 비동기 이터레이터, for/await 루프 도입

## 13.1 콜백과 비동기 프로그래밍
- JS 의 가장 기본적인 비동기 프로그래밍 -> 콜백

### 13.1.1 타이머
`setTimeout(fn, ms)`, setInterval(fn, ms)
일정 시간이 지나면 코드를 실행하는 단순한 비동기 프로그램 유형 중 하나.

### 13.1.2 이벤트
지정된 컨텍스트에 지정된 이벤트가 일어날 때마다 함수(이벤트 핸들러, 이벤트 리스너)를 호출.

### 13.1.3 네트워크 이벤트
XMLHttpRequest 클래스와 콜백 함수를 사용해 HTTP 요청을 보내고 서버의 응답을 비동기적으로 처리

### 13.1.4 노드의 콜백과 이벤트
노드(서버사이드 JS 환경)는 비동기적으로 만들어져 있으며 많은 API가 콜백과 이벤트를 사용한다. (ex. 파일 콘텐츠 읽고 쓰기 등)

노드에서는 on() 메서드로 이벤트 핸들러를 등록한다.

> https 내장 모듈을 이용해 http 통신하는 예제
```js
const https = require('https');

// URL의 텍스트 콘텐츠를 읽고 비동기적으로 콜액에 전등립니다.
function getText(url, callback) {
  // URL에 HTIP GET 요청을 시작합니다.
  const request = https.get(url);
  
  // 응답 이벤트를 처리할 함수를 등록합니다.
  request.on('response', response => {
    // 응답 이벤트가 있다는 것은 응답 헤더를 받았다는 의미입니다.
    let httpStatus = response.statusCode;
    // HTIP 응답의 바디는 아직 받지 못했으므로
    // 바디를 받았을 때 호출할 이벤트 핸들러를 등록합니다.
    response.setEncoding('utf-8'); // 유니코드 텍스트를 예상합니다.
    let body = ''; // 텍스트는 이 변수에 누적됩니다.
    // 바디의 텍스트 덩어리를 사용할 수 있게 되면 이 이벤트 핸들러를 호출합니다.

    response.on('data', chunk => {
      body += chunk;
    });
    
    // 응답이 완료되면 이 이벤트 핸들러를 호출합니다.
    response.on('end', () => {
      if (httpStatus === 200) {
        // HTIP 응답이 OKi라면
        callback(null, body); // 응답 바디를 콜백에 전달합니다.
      } else {
        // 그렇지 않다면 에러를 전달합니다.
        callback(httpStatus, null);
      }
    });
  });

  // 저수준 네트워크 에러를 처리할 이벤트 핸들러도 등록합니다.

  request.on('error', err => {
    callback(err, null);
  });
}
```


## 13.2 프라미스
> Promise : 비동기 작업의 결과를 나타내는 객체

**콜백의 문제점 개선**
1. 가독성: 콜백의 중첩(콜백 헬)을 좀 더 선형에 가까운 프라미스 체인으로 바꿈
2. 에러 처리: 최초 실행자에게 에러를 전달할 수 없던 콜백의 문제 해결

### 13.2.1 프라미스 사용
then()
- 프라미스 반환 함수 호출에 직접 이어붙이는 형태로 사용
- 프라미스 반환 함수, 결과를 사용하는 함수 모두 이름을 동사 형태 사용

에러 처리
- catch()

용어 정리
- fulfill 혹은 reject, : 작업의 성공 여부
- pending 혹은 settled : 작업의 완료 여부

### 13.2.2 프라미스 체인

- 메서드 중첩: 표현식 하나에서 메서드를 하나 이상 호출하는 것
```js
fetch (" /api/user/profi le")
	.then(response => { return response.json(); })
	.then(profile => { displayUserProfile(profile); }) ;
```

- 프라미스의 핵심 장점 -> 콜백 헬 방지
- 위의 메서드 중첩을 다음과 같이 깔끔하게 쓰자
```js
fetch(theURL) // 작업 1. 프라미스 을 반환 
	.then(callbackl) // 작업 2. 프라미스 를 반환 
	.then(callback2); // 작업 3. 프라미스 을 반환
```

### 13.2.3 프라미스 해석
- 프라미스 체인이 완전히 끝나기 전까지는 중간 콜백 함수의 반환 값을 담은 프로미스 객체를 전달한다. 이렇게 다음 프라미스로 잘 전달되면 해석, 아니면 거부된다. 완료는 모든 프라미스 체인이 끝난 경우된다. 

- 모든 프라미스 체인은 연결되었다.

### 13.2.4 프라미스와 에러
- catch()
- finally()
```js
startAsyncOperation( )
	.then(doStageTwo) 
	.catch (recoverFromStageToError) 
	.then(doStageThree) 
	.then(doStageFour) 
	.catch(logStageThreeAndFourErrors);
```

- .catch()에 전달하는 콜백은 이전 단계에서 에러가 일어났을 때만 호출된다. 이 곳에 전달된 에러는 픞라미스 체인을 타고 내려가지 않는다. (410p)
- catch의 콜백함수가 완료된 후에는 그 반환 값이 프라미스 체인을 따라 다음 프라미스로 넘어간다.

