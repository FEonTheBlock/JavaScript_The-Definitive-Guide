# 15 | 웹 브라우저의 자바스크립트 (part4)

## 네트워크

> 브라우저는 웹 페이지를 불러올 때마다 HTTP나 HTTPS 프로토콜을 사용해 네트워크 요청을 보내 HTML 파일과 이미지, 폰트, 스크립트, 스타일시트 등을 가져온다.

### fetch()

> 기본적인 HTTP 요청에서 `fetch()`는 3단계로 동작한다.

1. 콘텐츠를 가져올 URL을 전달하면서 `fetch()`를 호출한다.
2. HTTP 응답이 도착하기 시작하면 1단계에서 비동기적으로 반환한 응답 객체를 가져오고 이 응답 객체의 메서드를 호출해 응답 바디를 가져온다.
3. 2단계에서 비동기적으로 반환한 바디 객체를 사용해 필요한 일을 한다.

- `fetch()` API는 완전히 프라미스 기반이고 비동기 단계가 두 단계 있다.

```js
fetch('/api/users/current')
  .then((res) => res.json())
  .then((currentUser) => {
    displayUserInfo(currentUser);
  });

// or

async function asyncFetch() {
  const res = await fetch('/api/users/current');
  const currentUser = await res.json();
  displayUserInfo(currentUser);
}
```

### XMLHttpRequest, 이젠 안녕

`fetch()` API는 XML과는 아무 상관도 없으면서 이상한 이름이 붙은, 오래된 API인 `XMLHttpRequest`를 대체한다.

### HTTP 상태코드, 응답 헤더, 네트워크 에러

- `fetch()`가 반환하는 프라미스는 응답 객체로 해석된다.
- 위 객체의 `status` 프로퍼티는 HTTP 상태 코드이다.
- 위 객체의 `ok` 프로퍼티는 `status`가 200~299 사이의 숫자일때만 true이고 나머지는 false이므로 편리하게 사용할 수 있다.
- `fetch()`는 HTTP 상태와 응답 헤더를 받는 즉시 프라미스를 해석(`resolve`)한다.
- 이 시점은 응답 바디 전체가 도착하기 전이다.
- 이 단계에서 헤더는 확인할 수 있다.

### 요청 매개변수 설정

> 요청을 보내며 `URL`과 함께 매개변수를 전달해야 한다면 `URL` 마지막 `?` 뒤에 이름 값 쌍을 추가하면 된다.

```js
async function search(term) {
  const url = new URL('/api/search');
  url.searchParams.set('q', term);
  const res = await fecth(url);
  if (!res.ok) throw new Error(res.statusText);
  const resultsArray = await res.json();
  return resultArray;
}
```

### 요청 헤더 설정

```js
const authHeaders = new Headers();

// HTTPS 연결이 아니라면 기본 인증을 쓰면 안 된다.
authHeaders.set('Authorization', `Basic ${btoa(`${username}:${password}`)}`);
fetch('/api/users/', { headers: authHeaders })
  .then((res) => res.json())
  .then((usersList) => displayAllUsers(usersList));
```

### 응답 바디 분석

> `fetch()`의 두번째 단계는 응답 객체의 `json()`이나 `text()` 메서드를 호출해서 그 메서드가 반환하는 프라미스 객체를 반환하는 것이다.
> <br>이후 세번째 단계는 프라미스가 JSON 객체 또는 문자열로 파싱되며 시작한다.

- 응답 객체에는 `json()`, `text()` 외에도 세 가지 메서드가 더 있다.

1. `arrayBuffer()`

> 이 메서드는 `ArrayBuffer`로 해석되는 프라미스를 반환한다.
> <br>이진 데이터를 포함하는 응답을 받을 때 유용하다.

2. `blob()`

> 이 메서드는 `Blob` 객체로 해석되는 프라미스를 반환한다.
> <br>`Blob`은 거대한 이진 객체(`Binary Large Object`)의 약자이다.
> <br>`Blob`은 대량의 이진 데이터를 받을 때 적합하다.

3. `formData()`

> 이 메서드는 `FormData` 객체로 해석되는 프라미스를 반환한다.
> 이 메서드는 응답 바디가 `multipart/form-data` 형식으로 인코드됐다고 확신할때만 사용해야한다.

### 응답 바디 스트리밍

> 응답 바디를 비동기적으로 완료해 반환하는 다섯가지 메서드 외에 응답 바디를 스트리밍하는 방법도 있다.
> <br>이 방법은 응답 바디의 일부를 받을때마다 처리하는 형태로 사용할 수 있으며, 다운로드 진행 상태를 알리는 인터페이스를 제공할 때도 유용하다.

- 응답 객체의 body 프로퍼티는 `ReadableStream`객체이다.
- `text()`나 `json()`같은 응답 메서드를 이미 호출했다면 `body` 스트림이 이미 읽혔음을 뜻하는 `bodyUsed`가 `true`로 설정된다.
- `bodyUsed`가 `false`라면 아직 스트림을 읽지 않았다는 뜻이다.

### 요청 메서드와 요청 바디 지정

> 지금까지 설명한 `fetch()` 예제에서는 `HTTP`나 `HTTPS GET`요청을 사용했다.
> <br>`POST`, `PUT`, `DELETE`같은 요청 메서드를 사용하려면 `fetch()` 두번째 인자로 설정 객체를 전달하면서 그 객체에 `method` 프로퍼티를 넣으면 된다.

```js
fetch(url, {
  method: 'POST',
  headers: new Headers({ 'Content-Type': 'application/json' }),
  body: JSON.stringfy(reqBody),
})
  .then((res) => res.json())
  .then(handleRes);
```

### fetch()를 이용한 파일 업로드

> 사용자의 컴퓨터에서 웹 서버로 파일을 업로드할 때는 `FormData`객체를 요청 바디로 사용한다.
> <br>파일 객체를 얻을 때는 웹 페이지에 `<imput type="file">`요소를 사용하고 이 요소에 `change` 이벤트 리스너를 등록하는 방법을 주로 사용한다.

### 요청 취소

> 사용자가 취소 버튼을 클릭하거나 요청이 너무 오랫동안 수행될 경우 `fetch()` 요청을 취소해야 할 수도 있다.
> <br>`fetch API`는 `AbortController`와 `AbortSignal` 클래스를 사용해 요청을 취소할 수 있다.

```js
// 이 함수는 fetch()와 비슷하지만 설정 객체에 timeout 프로퍼티를 지원한다.
// 이 프로퍼티에 밀리초 단위로 지정한 시간 안에 요청이 완료되지 않으면 요청을 취소한다.
function fetchWithTimeout(url, options = {}) {
  if (options.timeout) {
    const controller = new AbortController();
    options.signal = controller.signal;

    setTimeout(() => {
      controller.abort();
    }, options.timeout);
  }
  return fetch(url, options);
}
```

### 소소한 요청 옵션

**`cache`**

> 브라우저의 기본 캐싱 동작을 덮어 쓴다.
> <br>유효한 값은 아래와 같다.

- "default": 기본 캐싱 동작을 지정하는 값
- "no-store": 브라우저가 캐시를 무시하게 한다.
- "reload": 브라우저가 항상 캐시를 무시하고 네트워크 요청을 보내게 한다, 하지만 응답을 받으면 캐시에 저장한다.
- "no-cache": 이 값은 캐시에 저장된 값이 오래됐든 그렇지 않든 항상 확인 후에 전송한다.
- "force-cache": 캐시에 저장된 값이 오래됐더라도 항상 캐시의 값을 전송한다.

**`redirect`**

> 서버에서 보내는 리다이렉트 응답을 처리할 방법을 지정한다.

- "follow": 기본 값이며, 서버의 리다이렉트 응답을 그대로 따른다, 이 값을 사용하면 `fetch()`에서 가져오는 응답 객체에 300-399 범위의 `status` 값이 있어서는 안된다.
- "error": 서버가 리다이렉트 응답을 반환할 경우 프라미스를 거부한다.
- "manual": 리다이렉트 응답을 직접 처리하겠다는 의미

**`referer`**

> 이 프로퍼티 값은 `HTTP Referer`헤더의 값을 지정하는 상대 `URL`을 포함하는 문자열이다.
> <br>이 프로퍼티를 빈 문자열로 설정하면 요청에서 `Referer`헤더는 생략한다.

### 서버 전송 이벤트

> HTTP 프로토콜의 기본적인 특징은 클라이언트가 요청을 보내고 서버가 이 요청에 응답한다는 것이다.
> <br>웹은 이 특징을 기초로 만들어졌다.
> <br>하지만 일부 웹 애플리케이션에서는 어떤 이벤트가 있을 때마다 서버가 알림을 보내는게 적합할 때도 있다.
> <br>HTTP에는 이를 지원하는 기능이 없지만, 클라이언트가 서버에 요청을 보내면 클라이언트와 서버 어느쪽에서도 연결을 끊지 않는다는 점을 이용한 일종의 편법이다.

- 웹 서버에 이런 지속성 요청을 보낼 때는 `EventSource()`생성자에 URL을 전달하기만 하면 된다.
- 서버가 그 연결에 적절한 형태의 데이터를 기록하면 `EventSource`객체는 그 데이터를 이벤트로 일으킨다.

```js
const ticker = new EventSource('stockprices.php');
ticker.addEventListener('bid', (event) => {
  displayNewBid(event.data);
});
```

- 서버 전송 이벤트 프로토콜은 단순하다.

1. 클라이언트가 `EventSource` 객체를 생ㅇ성해 서버에 연결을 보내면 서버는 이 연결을 열어둔다.
2. 이벤트가 일어나면 서버는 이 연결에 텍스트를 보낸다.

- 서버 전송 이벤트의 가장 확실한 예제는 온라인 채팅처럼 사용자 여럿이 참여하는 애플리케이션이다.
  - 채팅 클라이언트는 `fetch()`를 통해 채팅방에 메시지를 보낸다.
  - 이후 `EventSource`객체를 통해 스트림을 구독한다.

### EventSource를 사용한 단순한 채팅 클라이언트

```js
// UI 세부 사항 일부
const nick = prompt('Enter your nickname');
const input = document.getElementById('input');
input.focus();

// EventSource를 써서 새로운 메시지 알림을 등록
const chat = new EventSource('/chat');
chat.addEventListener('chat', (event) => {
  const div = document.createElement('div');
  div.append(event.data);
  input.before(div);
  input.scrollIntoView();
});

// fetch()를 써서 사용자의 메시지를 서버에 보냅니다.
input.addEventListener('change', () => {
  fetch('/chat', {
    method: 'POST',
    body: `${nick}: ${input.value}`,
  }).catch((e) => console.error);
  input.value = '';
});
```

### 서버 전송 이벤트 채팅 서버

```js
// 이 코드는 노드에서 실행하도록 만든 서버 사이드 프로젝트이며
// 아주 단순하고 완전히 익명인 채팅방을 만듭니다.
// /chat에 새로운 메시지를 POST로 보내거나 GET으로 text/event-stream 메시지를 받습니다.
// /에 GET 요청을 보내면 클라이언트 사이드 채팅 UI가 포함된 단순한 HTML 파일을 반환합니다.

const http = require('http');
const fs = require('fs');
const url = require('url');

// 아래에서 사용할 채팅 클라이언트 HTML 파일
const clientHTML = fs.readFileSync('chatClient.html');

// 이벤트를 보낼 ServerResponse 객체 배열
const clients = [];

// 새로운 서버를 생성하고 포트 8080을 사용합니다.
// http://localhost:8080/
const server = new http.Server();
server.listen(8080);

// 서버는 새로운 요청을 받으면 이 함수를 실행합니다.
server.on('request', (req, res) => {
  // 요청받은 URL을 분석합니다.
  const pathname = url.parse(req.url).pathname;

  // 요청이 /라면 클라이언트 사이드 채팅 UI를 전송합니다.
  if (pathname === '/') {
    res.writeHead(200, { 'Content-Type': 'text/html' }).end(clientHTML);
  }

  // /chat 경로가 아니거나 메서드가 GET, POST가 아니라면 404에러를 전송합니다.
  else if (
    pathname !== '/chat' ||
    (req.method !== 'GET' && req.method !== 'POST')
  ) {
    res.writeHead(404).end();
  }
  // /chat에 GET 요청이 들어왔다면 클라이언트가 연결하는 겁니다.
  else if (req.method === 'GET') {
    acceptNewClient(req, res);
  }
  // 아니라면 /chat 요청은 새로운 메시지를 보내는 POST 요청입니다.
  else {
    broadcastNewMessage(req, res);
  }
});

// 클라이언트가 새로운 EventSource 객체를 생성할 때, 또는 EventSource가 자동으로 다시 연결될 때 생성되는 /chat 엔드포인트의 GET 요청을 처리합니다.
function acceptNewClient(req, res) {
  // 나중에 메시지를 보낼 수 있도록 응답 객체를 기억합니다.
  clients.push(res);

  // 클라이언트가 연결을 끊으면 클라이언트 배열에서 해당 클아이언트의 응답 객체를 제거합니다.
  req.connection.on('end', () => {
    clients.splice(clients.indexOf(res), 1);
    res.end();
  });

  // 헤더를 설정하고 이 클라이언트에 초기 채팅 이벤트를 전송합니다.
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    Connection: 'keep-alive',
    'Cache-Control': 'no-cache',
  });
  res.write('event: chat/ndata: Connected\n\n');

  // 여기서는 의도적으로 res.end()를 호출하지 않았습니다.
  // 서버 전송 이벤트는 연결을 계속 유지해야 합니다.
}

// 이 함수는 사용자가 새로운 메시지를 보내는 /chat 엔드포인트에 대한
// POST 요청의 응답으로 호출됩니다.
async function broadcastNewMessage(req, res) {
  // 먼저 요청 바디를 읽어 사용자의 메시지를 가져옵니다.
  req.setEncoding('utf8');
  let body = '';
  for await (let chunk of req) {
    body += chunk;
  }

  // 바디를 읽으면 빈 응답을 보내고 연결을 닫습니다.
  res.writeHead(200).end();

  // 메시지를 text/event-stream 형식으로 바꾸고 각 행 앞에 "data: "를 붙입니다.
  let message = 'data: ' + body.replace('\n', '\ndata: ');

  // 메시지 데이터 앞에 채팅 이벤트임을 뜻하는 전치사를 붙이고 뉴라인 두개를 이어붙여
  // 이벤트가 끝났음을 알립니다.
  let event = `event: chat\n${message}\n\n`;

  // 연결된 클라이언트 전체에 이 이벤트를 전송합니다.
  clients.forEach((client) => client.write(event));
}
```

### 웹 소켓

> 웹 소켓 API는 복잡하고 강력한 네트워크 프로토콜의 단순한 인터페이스이다.
> <br>브라우저는 웹 소켓을 통해 텍스트나 이진 메시지를 서버와 쉽게 주고받을 수 있다.
> <br>서버 전송 이벤트와 마찬가지로 클라이언트가 연결을 시작해야 하지만
> <br>일단 연결이 되면 서버 전송 이벤트와 달리 이진 메시지를 지원하고 양방향 통신이 가능하다.

- 웹 소켓을 지원하는 네트워크 프로토콜은 일종의 HTTP 확장이다.
- 웹 소켓 프로토콜을 사용하는 서비스에 연결할 때는 일반적인 웹 서비스 마찬가지로 URL을 사용하지만 URL 스키마가 `https://`대신 `wss://`로 시작한다.

### 웹 소켓 생성, 연결, 연결 끊기

> 웹 소켓 서버와 통신하려면 서버와 서비스를 나타내는 `wss://` URL을 전달해 웹 소켓 객체를 만든다.

```js
let socket = new WebSocket('wss://example.com/stockticker');
```

웹 소켓 객체를 만들면 연결 프로세스는 자동으로 시작되지만 새로 생성한 웹소켓이 즉시 연결되지는 않는다.

### 웹 소켓으로 메시지 전송

> 웹 소켓으로 연결된 서버에 메시지를 보낼 때는 웹소켓 객체의 `send()` 메서드를 호출하기만 하면 된다.
> <br>`send()`는 문자열, 블롭, ArrayBuffer, 형식화 배열, DataView 중 하나를 메시지 인자로 받는다.

### 웹 소켓 메시지 수신

> 웹 소켓을 통해 서버 메시지를 수신할 때는 `message` 이벤트에 이벤트 핸들러를 등록한다.
> <br>`message`이벤트의 이벤트 객체는 `MessageEvent`인스턴스이며 이 인스턴스의 `data`프로퍼티에 서버의 메시지가 포함되어 있다.
> <br>서버가 `UTF-8`로 인코드된 텍스트를 보낸다면 `event.data`에 이 텍스트가 포함된다.

### 프로토콜 교섭

> 웹 소켓 프로토콜은 텍스트와 이진 메시지를 주고받지만 그 메시지의 구조나 의미에 대해서는 아무것도 주고받지 않는다.
> <br>애플리케이션에서 웹 소켓을 사용하려면 반드시 웹 소켓 기반 통신 프로토콜을 만들어야 한다.

## 스토리지

> 웹 애플리케이션은 브라우저 API를 사용해 사용자의 컴퓨터에 데이터를 저장할 수 있다.

### 웹 스토리지

> 웹 스토리지 API는 `localStorage`, `sessionStorage`객체로 구성된다.
> <br>이들은 기본적으로 문자열 키와 문자열 값을 연결한 지속성 있는 객체이다.

### 쿠키

> 쿠키는 원래 서버 사이드 스크립트에서 사용하도록 설계된 메커니즘이다.
> <br>클라이언트 사이드에서 쿠키를 다룰 수 없는건 아니지만, 쉽지는 않으며 텍스트 데이터를 소량 저장하는 정도로만 쓸 수 있다.
> <br>또한 쿠키에 저장된 데이터는 서버에서 쓸모가 없더라도 HTTP 요청이 있을 때마다 항상 서버로 전송된다.

### indexedDB

> `indexDB`는 인덱스를 지원하는 객체 데이터베이스의 비동기 API이다.

### 로컬스토리지와 세션스토리지

> Window 객체의 `localStorage`, `sessionStorage` 프로퍼티는 스토리지 객체를 참조한다.
> <br>스토리지 객체는 일반적인 JS 객체와 거의 비슷하지만 다음과 같은 차이가 있다.

- 스토리지 객체의 프로퍼티 값에는 문자열만 쓸 수 있다.
- 스토리지 객체에 저장된 프로퍼티에는 지속성이 있다.

### 스토리지 수명과 스코프

- localStorage와 sessionStorage는 문서 출처에 종속된다.
- localStorage는 브라우저에도 종속된다.

  - 예를 들어 한사이트를 파이어폭스로 방문했다가 다시 크롬으로 방문했다면, 처음 방문했을 때 저장한 데이터를 두번째 방문에서 사용할 수는 없다.

- sessionStorage에 저장된 데이터는 최상위 창이나 브라우저 탭이 닫힐 때 사라진다.
- 하지만 최신 브라우저에는 최근에 닫은 탭을 다시 열고 마지막 세션을 복원하는 기능이 있으므로 이런 탭에 저장된 데이터는 더 오래 지속될 수 있다.

### 쿠키

> 쿠키는 특정 웹 페이지 또는 웹 사이트와 연관된 소량의 이름 붙은 데이터이며 웹 브라우저에 저장된다.

### 쿠키라는 이름의 유래

> `쿠키` 또는 `매직 쿠키`라는 용어는 컴퓨팅 분야에서 오래 전부터 접근을 식별하거나 허용하는 데이터의 작은 덩어리, 특히 개인적이거나 비밀스러운 데이터를 가리키는 용도를 쓰였다.

## 워커 스레드와 메시지

> JS의 가장 기본적인 특징 중 하나는 싱글 스레드라는 점이다.

웹 브라우저는 `Worker` 클래스의 싱글 스레드 요건을 아주 조심스럽게 완화한다.

- `Worker`클래스의 인스턴스는 메인 스레드, 이벤트 루프와 동시에 실행되는 스레드이다.
- 워커는 비동기 메시지 전달을 통해서만 메인 스레드와 통신할 수 있다.
- 따라서 워커가 DOM을 즉시 수정하는 것은 불가능하지만 오랫동안 실행되는 함수가 이벤트 루프를 차단하는 일을 막을 수 는 있다.

### `Worker` 객체

> 새로운 워커를 생성할 때는 워커가 실행할 JS 코드를 URL로 전달하면서 `Worder()` 생성자를 호출합니다.

```js
const dataCruncher = new Worker('utils/cruncher.js');

dataCruncher.postMessage('/api/data/to/crunch');
dataCruncher.onmessage = function (e) {
  const stats = e.data;
  console.log(`Average: ${stats.mean}`);
};
```

### 워커의 전역 객체

> `Worder()` 생성자로 새로운 워커를 생성할 때는 JS 코드 파일의 URL을 전달한다.
> <br>이 코드는 워커를 생성한 스크립트에서 분리된, 새롭고 깨끗한 JS 실행 환경에서 실행된다.
> <br>이 새로운 실행 환경의 전역 객체가 `Worder-GlobalScope` 객체이다.

### 워커로 코드 가져오기

> 워커는 JS에서 모듈 시스템을 수용하기 전에 정의됐으므로 워커만의 가져오기 시스템이 있다.
> <br>`WorkerGlobalScope`에는 모든 워커에서 접근할 수 있는 `importScripts()`전역 함수가 있다.

```js
importScripts('utils/Histogram.js', 'utils/BitSet.js');
```

### 워커의 모듈

> 워커에서 모듈을 사용하려면 반드시 `Worder()` 생성자에 두번째 인자를 전달해야 한다.
> <br>두번째 인자는 반드시 `type` 프로퍼티가 문자열 `module`인 객체여야 한다.
> <br>`Worder()` 생성자에서 `type:"module"`옵션은 HTML `<script>` 태그의 `type="module"` 속성과 마찬가지로 이 코드를 모듈로 해석하며 `import` 선언을 허용하라는 의미이다.
