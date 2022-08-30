## 16 | 노드와 서버 사이드 자바스크립트 (part2)

## 파일 작업

> 노드의 fs 모듈은 파일과 디렉터리를 다루는 종합적인 API이다.
> <br>`path`모듈은 파일과 디렉터리 이름을 다루는 유틸리티 함수를 정의해 fs 모듈을 보조한다.

### 경로, 파일 서술자, 파일 핸들

> fs 모듈의 함수로 파일 작업을 하려면 먼저 작업할 파일을 지정해야 한다.
> <br>파일은 대부분 경로로 지정한다.

```js
// 몇 가지 중요한 경로
process.cwd(); // 현재 작업 디렉터리의 절대 경로
__filename; // 현재 코드를 담은 파일의 절대 경로
__dirname; // __filename을 포함하는 디렉터리의 절대 경로
os.homedir(); // 사용자의 홈 디렉터리

const path = require('path');

path.sep; // 운영 체제에 따라 / 또는 \
```

### 파일 읽기

> 노드에서는 파일 콘텐츠를 한 번에 읽을 수도, 스트림을 통해 읽을 수도, 저수준 API를 이용해 읽을 수도 있다.
> <br>파일이 작거나 메모리/성능 문제가 없다면 파일 콘텐츠 전체를 한 번에 읽는 게 가장 편리하다.

```js
const fs = require('fs');
let buffer = fs.readFileSync('test.data'); // 동기적, 버퍼를 반환
let text = fs.readFileSync('data.csv', 'utf8'); // 동기적, 문자열을 반환

// 파일 바이트를 비동기적으로 읽음
fs.readFile('test.data', (err, buffer) => {
  if (err) {
    // 에러 처리
  } else {
    // 파일 콘텐츠는 버퍼에 있음
  }
});

// 프라미스 기반 비동기 일기
fs.promises
  .readFile('data.csv', 'utf8')
  .then(processFileText)
  .catch(handleReadError);

// 또는 비동기 함수 안에서 await과 함께 프라미스 API 사용
async function processText(filename, encoding = 'utf8') {
  let text = await fs.promises.readFile(filename, encoding);
}
```

### 파일 쓰기

> 노드에서 파일 쓰기는 파일 읽기와 비슷하지만 추가로 알아야 할 점이 좀 있다.
> <br>그 중 하나는 존재하지 않는 파일 이름을 사용해 파일을 쓰면 새로운 파일이 자동으로 만들어진다는 점

**파일 쓰는 법**

- 파일 콘텐츠 전체가 문자열이나 버퍼에 들어 있다면 콜백 기반인 `fs.writeFile()`, 동기적인 `fs.writeFileSync()`, 프라미스 기반인 `fs.promises.writeFile()`을 한번만 호출해서 전체 내용을 기록할 수 있다.

### 파일 메타데이터

> `fs.stat()`, `fs.statSync`, `fs.promises.stat()` 함수는 지정된 파일이나 디렉터리의 메타데이터를 반환한다.

```js
const fs = require('fs');
let stats = fs.statSync('book/ch15.md');

stats.isFile(); // => true: 일반적인 파일
stats.isDirectory; // => false: 디렉터리가 아님
stats.size; // 파일 크기(바이트 단위)
stats.atime; // 접근 시간: 마지막으로 파일을 읽은 시간
stats.mtime; // 변경 시간: 마지막으로 기록한 시간
stats.uid; // 파일 소유자의 id
stats.gid; // 파일 소유자의 그룹 id
stats.mode.toString(8); // 파일 퍼미션(8진수 문자열)
```

### 디렉터리 작업

> 노드에서 새로운 디렉터리를 생성할 때 `fs.mkdir()`, `fs.mkdirSync()`, `fs.promises.mkdir()`를 사용한다.
> <br>이들 함수의 첫 번째 인자는 생성할 디렉터리의 경로이다.
> <br>`option`의 두 번째 인자는 새로운 디렉터리의 모드(퍼미션 비트)를 지정하는 정수이다.

```js
// dist/와 dist/lib/을 모두 확인하고 존재하지 않으면 생성
fs.mkdirSync('dist/lib', { recursive: true });
```

## HTTP 클라이언트와 서버

> 노드의 `http`, `https`, `http2` 모듈은 `HTTP` 프로토콜의 모든 기능을 갖추긴 했지만 비교적 저수준으로 구현되었다.
> <br>이들 모듈은 `HTTP` 클라이언트와 서버를 만들 수 있는 포괄적인 API를 제공한다.
> <br>노드의 내장 모듈만 사용해도 단순한 `HTTP`, `HTTPS` 서버를 만들 수 있다.
> <br>하지만 일반적으로 실무 서버를 이런 모듈만 가지고 만드는 일은 거의 없다.(통상적으로 외부 라이브러리 사용[`express` 등등])

## 워커 스레드

> 노드의 동시성 모델은 싱글 스레드이며 이벤트 기반이다.
> <br>하지만 노드 버전 10부터는 웹 브라우저의 웹 워커 API를 거의 그대로 반영한 멀티스레드 프로그래밍 모델 역시 지원한다.
> <br>멀티스레드 프로그래밍은 어렵기로 악명 높은데, 여러 스레드가 공유 메모리에 접근하는 것을 주의 깊게 조율해야 하기 때문인 경우가 대부분이기 때문이다.
> <br>하지만 JS 스레드는 노드나 브라우저 모두 기본적으로 메모리를 공유하지 않으므로 멀티스레드 프로그래밍의 위험성이나 어려움은 JS 워커에는 적용되지 않는다.
