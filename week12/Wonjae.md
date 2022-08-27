# 16 | 노드와 서버 사이드 자바스크립트

> 노드는 운영 체제와 연결된 JS이며 JS 프로그램에서 파일을 읽고 쓰고, 자식 프로세스를 실행하고, 네트워크를 통해 통신할 수 있게 한다.

### 노드의 용도

- bash나 기타 유닉스 셸의 복잡한 문법에서 해방된 최신 셸 스크립트
- 신뢰할 수 없는 코드를 다루는 웹 브라우저에 적용되는 보안 제한에서 벗어나, 신뢰할 수 있는 프로그램을 실행하는 범용 프로그래밍 언어
- 효율적이고 병렬화된 웹 서버 환경

### 노드의 특징

- 태생부터 비동기적인 API를 바탕으로 한 싱글 스레드의 이벤트 기반 병렬 환경

## 노드 프로그래밍 기본

### 콘솔 출력

```js
console.log('Hello World!');
```

표준 출력을 사용하는 더 저수준의 메서드가 없는 건 아니지만 가장 쉽고 공식적인 방법은 `console.log()`이다.

- `console.error()`는 표준 에러(`stderr`) 스트림에 기록한다는 점이 `console.log()`와 다르다.

### 명령행 인자와 환경 변수

> 노드는 유닉스와 비슷하게 명령행 인자와 환경 변수를 통해 입력을 받는다.
> <br>노드 프로그램은 문자열 배열인 `process.argv`에서 명령행 인자를 읽을 수 있다.

```js
console.log(process.argv);
```

```shell
$ node --trace-uncaught argv.js --arg1 --arg2 filename
[
  '/usr/local/bin/node',
  '/private/tmp/argv.js',
  '--arg1',
  '--arg2',
  'filename'
]
```

- `process.argv`의 첫번째와 두번째 요소는 항상 노드 실행 파일과 JS 파일의 절대 경로이며, 명령행에 어떻게 타이핑했는지는 중요하지 않다.
- 노드 실행 파일 자체에서 사용하는 명령행 인자는 `process.argv`에 포함되지 않는다.

### 노드 모듈

> node는 JS에서 모듈 시스템을 정립하기 전에 만들어졌으므로 그들만의 체계화된 모듈 시스템을 갖고 있다.
> <br>node의 모듈 시스템은 `require()`함수로 모듈에 값을 가져오고, `exports`객체나 `module.exports` 프로퍼티로 값을 내보낸다.

- node 13부터는 그동안 사용하던 `require` 기반 모듈(노드에서는 CommonJS 모듈이라 부른다.) 외에도 ES6 모듈 표준도 추가로 지원한다.
- 두 모듈 시스템은 완전히 호환되지 않으므로 이들을 함께 사용하기는 조금 까다롭다.
- 모듈 종류를 노드에 알리는 가장 단순한 방법은 파일 확장자로 구분하는 것이다.
  - `.mjs`: ES6모듈(`import`, `export`)
  - `.cjs`: CommonJS모듈(`require()`)
- 파일 확장자에 `.mjs`, `.cjs`를 쓰지 않았다면 노드는 파일과 같은 디렉터리, 부모 디렉터리에서 `package.json`파일을 찾는다.
- 가장 가까운 `package.json`파일을 찾으면 노드는 `JSON`객체에서 최상위 `type`프로퍼티를 체크한다.
- `type`프로퍼티가 없으면 `default`로 `commonjs` 모듈로 간주한다.

### 노드 패키지 매니저

> 노드를 설치하면 일반적으로 `npm`이라는 프로그램이 함께 설치된다.
> <br>`npm`은 노드 패키지 매니저(`Node Package Manager`)의 축약어이다.
> <br>`npm`은 프로그램에 필요한 라이브러리를 내려받고 관리하는 일을 돕는다.

## 노드는 기본적으로 비동기적이다.

> JS는 범용 프로그래밍 언어이므로 거대한 행렬 연산이나 복잡한 통계 분석같은 CPU 집약적인 프로그램도 얼마든지 작성할 수 있다.
> <br>반면 노드는 네트워크 서버처럼 입출력 집약적인 프로그램에 맞게 설계되고 최적화되었다.
> <br> 특히 노드는 최대한 많은 요청을 동시에 처리할 수 있는 서버를 쉽게 만들 수 있도록 설계되었다.

- 노드는 대부분의 프로그래밍 언어와 달리 동시성을 스레드로 구현하지 않았다.
- 멀티스레드는 정확하게 프로그래밍하기 어렵고, 디버깅도 어렵다.
- 또한 스레드는 비교적 무거운 작업이므로 수백개의 스레드를 사용해 수백개의 요청을 동시에 처리하려면 엄청난 메모리가 필요하다.
- 노드는 JS의 싱글스레드 프로그래밍 모델을 받아들였다.
  - 전문 지식을 오랫동안 공부하지 않고도 네트워크 서버를 만들 수 있을 정도로 단순화하기 위해서였다.

## 버퍼

> 버퍼 클래스는 파일이나 네트워크에서 데이터를 읽을 때 특히 자주 사용하는 데이터 타입이다.

- 버퍼는 문자열과 아주 비슷하지만 문자 시퀀스가 아닌 바이트 시퀀스라는 점이 다르다.
- 현재는 부호 없는 바이트 배열을 나타내는 `Unit8Array`의 서브 클래스이다.

## 이벤트와 이벤트이미터

```js
const EventEmitter = require('events');
const net = require('net');
let server = new net.Server(); // 서버 객체 생성
server instanceof EventEmitter; // true: 서버는 이벤트이미터이다.
```

- 이벤트이미터의 핵심 기능은 `on()`메서드를 통해 이벤트 핸들러 함수를 등록하는 것이다.
- 이벤트이미터는 여러 타입의 이벤트를 방사할 수 있으며, 이벤트 타입은 이름으로 구분된다.

```js
const net = require('net');
let server = new net.Server();

server.on('connection', (socket) => {
  // 서버 connection 이벤트는 연결된 클라이언트에 해당하는 소켓 객체를 전달받는다.
  // 여기서는 클라이언트에 약간의 데이터를 전송하고 연결을 끊는다.
  socket.end('Hello World', 'utf8');
});
```

## 스트림

> 데이터를 처리하는 알고리즘을 구현할 때는 데이터 전체를 메모리에 읽어들이고, 이를 처리한 다음 데이터를 내보내는 방식이 항상 가장 쉽다.

```js
const fs = require('fs');

// 비동기적이지만 스트림을 사용하지는 않는 (그래서 비효율적인) 함수
function copyFile(sourceFilename, destinationFilename, callback) {
  fs.readFile(sourceFilename, (err, buffer) => {
    if (err) {
      callback(err);
    } else {
      fs.writeFile(destinationFilename, buffer, callback);
    }
  });
}
```

- 위 `copyFile()`함수는 비동기 함수와 콜백을 사용하므로, 차단적인 함수가 아니며 서버처럼 동시성이 중요한 프로그램에 알맞다.
- 하지만 이 함수는 반드시 파일의 콘텐츠 전체를 담기에 충분한 메모리를 할당받아야 한다.
- 이렇게 구현하면 파일의 크기가 커진다면 복사에 실패할수도 있고 원본 파일을 다 읽기 전에는 사본 파일의 기록을 시작할 수 없다는 단점이 있다.
- 이러한 문제의 해결책으로 데이터가 프로그램으로 흘러들어와서 처리되고 다시 흘러나가는 스트리밍 알고리즘이다.

### 스트리밍 알고리즘

> 데이터를 항상 작은 덩어리로 처리하며 절대 데이터 전체를 한 번에 메모리에 담지 않는다.
> <br>노드의 네트워크 API는 스트리밍 기반이고 노드의 파일시스템 모듈 역시 파일을 읽고 쓰는 스트리밍 API를 갖추고 있으므로, 노드 프로그램을 만들다보면 스트리밍 API를 자주 사용하게 될 것이다.

**노드가 지원하는 네 가지 기본 스트리밍 타입**

- 리더블: 데이터 소스
- 라이터블: 데이터가 향하는 대상
- 듀플렉스: 리더블 스트림과 라이터블 스트림을 객체 하나에 조합한 것
- 트랜스폼: 읽고 쓸 수 있는 스트림이지만 듀플렉스 스트림과는 차이가 있다.
  - 트랜스폼 스트림에 출력된 데이터는 같은 스트림에서 읽을 수 있다.

### 파이프

> 때때로 스트림에서 데이터를 읽고, 같은 데이터를 다른 스트림에 쓰는 단순한 작업이 필요할 때도 있다.
> <br>예를 들어 정적 파일 디렉터리 역할을 하는 단순한 `HTTP` 서버를 만든다고 하면 이 서버는 파일 입력 스트림에서 데이터를 읽고, 네트워크 소켓에 출력해야한다.
> <br>하지만 데이터를 읽고 쓰는 코드를 직접 작성할 필요 없이 두 소켓을 `파이프`로 연결하기만 하면 나머지 복잡한 작업을 노드가 알아서 처리한다.

```js
const fs = require('fs');

function pipeFileToSocket(filename, socket) {
  fs.createReadStream(filename).pipe(socket);
}

function pipe(readable, writable, callback) {
  // 에러 처리 코드
  function handleError(err) {
    readable.close();
    writable.close();
    callback(err);
  }

  // 파이프를 정의하고 일반적인 상황을 처리
  readable
    .on('error', handleError)
    .pipe(writable)
    .on('error', handleError)
    .on('finish', callback);
}
```

## 프로세스, CPU, 운영 체제 세부 사항

> 전역 객체 `Process`에는 현재 실행 중인 노드 프로세스의 상태에 관련된 유용한 프로퍼티와 함수가 많이 있다.

```js
process.argv; // 명령행 인자 배열
process.arch; // x64같은 CPU 아키텍처
process.cwd(); // 현재 작업 디렉터리
process.chdir(); // 현재 작업 디렉터리 변경
process.cpuUsage(); // CPU 사용 현황 보고
process.env; // 환경 변수 객체
process.execPath; // 노드 실행 파일의 파일시스템 절대 경로
process.exit(); // 프로그램을 종료
process.exitCode; // 프로그램을 종료할 때 보고할 정수 코드
process.getuid(); // 현재 사용자의 유닉스 사용자 ID
process.hrtime.bigint(); // 정밀한 나노초 타임스탬프
process.kill(); // 다른 프로세스에 신호를 보냄
process.memoryUsage(); // 메모리 사용 현황을 객체로 반환
process.nextTick(); // setImmediate() 와 마찬가지로 합수를 곧 호출
process.pid; // 현재 프로세스의 프로세스 ID
process.ppid; // 부모 프로세스의 ID
process.platform; // 운영 체제. linux, darwin, win32 등
process.resourceUsage(); // 자원 사용 현황을 객체로 반환
process.setuid(); // ID나 이름으로 현재 사용자를 변경
process.title; // ps 리스트에 나타나는 프로세스 이름
process.umask(); // 새로운 파일에 대한 기본 퍼미션을 설정하거나 반환
process.uptime(); // 노드 실행시간을 초 단위로 반환
process.version; // 노드 버전 문자열
process.versions; // 노드가 사용하는 라이브러리의 버전 문자열
```
