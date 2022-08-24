# 정민 12주차 학습(16.6 까지)

> 자바스크립트 완벽가이드 16장에 해당하는 부분이고, 읽으면서 자바스크립트에 대해 새롭게 알게된 부분만 정리한 내용입니다.

노드는 운영 체제와 연결된 자바스크립트이며 자바스크립트 프로그램에서 **파일을 읽고 쓰고, 자식 프로세스를 실행하고, 네트워크를 통해 통신**할 수 있게 한다.

노드의 용도

- 배시나 기타 유닉스 셸의 복잡한 문법에서 해방된 최신 셸 스크립트
- 신뢰할 수 없는 코드를 다루는 웹 브라우저에 적용되는 보안 제한에서 벗어나, 신뢰할 수 있는 프로그램을 실행하는 범용 프로그래밍 언어
- 효율적이고 병렬화된 웹 서버 환경

> 노드의 가장 분명한 특징은 **비동기적인 API를 바탕으로 한, 싱글 스레드의 이벤트 기반 병렬 환경**이라는 것이다.

이 장에서 다루는 것

- 노드 프로그래밍 모델
- 노드의 동시성
- 스트리밍 데이터를 다루는 API
- 이진 데이터를 다루는 버퍼 타입
- 파일과 네트워크, 프로세스, 스레드를 다루는 몇 가지 가장 중요한 노드 API

## 노드 프로그래밍 기본

### 콘솔 출력

웹 브라우저의 console.log(), console.warn(), console.error()는 일반적으로 그 메시지 종류를 나타내는 작은 아이콘을 표시한다. 그러나 노드에는 이런 기능이 없고, **console.error()는 표준 에러(stderr) 스트림에 기록한다는 점**이 console.log()와 다르다. 프로그램에서 표준 출력을 파일이나 파이프에 리다이렉트한다면 console.log()의 텍스트는 사용자에게 보이지 않고 console.error()의 텍스트만 콘솔에 출력된다.

### 명령행 인자와 환경 변수

노드 프로그램은 문자열 배열인 process.argv에서 명령행 인자를 읽을 수 있다. 첫 번째 요소는 항상 노드 실행 파일의 경로이며, 두 번째는 노드가 실행하고 있는 자바스크립트 코드 파일 경로이다.

![](https://velog.velcdn.com/images/hustle-dev/post/cfc90397-aabb-4927-a33c-a4a61e44fc9d/image.png)

> 첫 번째와 두 번째는 항상 노드 실행 파일과 자바스크립트 파일의 절대 경로이다. 또한 --trace-uncaught와 같이 노드 실행 파일 자체에서 사용하는 명령행 인자는 process.env에 포함되지 않는다.

### 프로그램 수명

노드 프로그램은 대부분 비동기적이며 콜백과 이벤트 핸들러를 바탕으로 동작한다. 노드 프로그램은 초기 파일을 실행하고 콜백을 모두 호출한 뒤 남은 이벤트가 없을 때까지 종료되지 않는다.

> 네트워크 연결을 주시하는 노드 기반 서버 프로그램은 항상 이벤트를 기다리고 있으므로 이론적으로 영원히 실행된다.

프로그램에서 예외가 발생했을 때, 전역 핸들러 함수를 등록해 프로그램이 충돌하는 일을 막을 수 있다.

```js
process.setUncaughtExceptionCaptureCallback((e) => {
  console.error("Uncaught exception:", e);
});
```

유사하게 프라미스가 미처리 상태로 거부되어 에러 메시지를 출력하거나 프로그램을 종료하는 일을 막으려면 다음과 같이 전역 핸들러 함수를 등록하라.

```js
process.on("unhandledRejection", (reason, promise) => {
  // reason은 .catch() 함수에 전달된 값
  // promise는 거부된 프라미스 객체
});
```

## 노드는 기본적으로 비동기적입니다

노드는 네트워크 서버처럼 입출력 집약적인 프로그램에 맞게 설계되고 최적화되었다. 특히 최대한 많은 요청을 동시에 처리할 수 있는 서버를 쉽게 만들 수 있도록 설계되었다.

노드는 기본적으로 비동기, 비차단 방식 API를 채택하여 싱글스레드 프로그램이 모델을 유지하면서도 높은 수준의 동시성을 구현한다.
→ 네트워크 연결을 초기화하거나 파일의 마지막 변경 시간을 구하는 함수조차도 기본 함수는 비차단 방식으로 설계

노드는 프라미스 클래스를 도입하기 전에 만들어졌으므로 비동기 API는 콜백 기반이다.

> **오류 우선 콜백**을 사용!
> → 에러 인자를 첫 번째로 사용하는 이유는 이를 생략할 수 없게 강제하기 위해서

```js
const fs = require("fs");

function readConfigFile(path, callback) {
  fs.readFile(path, "utf8", (err, text) => {
    if (err) {
      console.error(err);
      callback(null);
      return;
    }
    let data = null;
    try {
      data = JSON.parse(text);
    } catch (e) {
      console.error(e);
    }
    callback(data);
  });
}
```

## 버퍼

버퍼 클래스는 파일이나 네트워크에서 데이터를 읽을 때 자주 사용한다. 버퍼가 Uint8Array와 다른 점은 자바스크립트 문자열과 상호 운용되도록 설계됐다는 점이다. 정상적으로 인코드된 바이트 시퀀스와 문자 인코딩이 주어지면 그 바이트를 문자 시퀀스로 디코드할 수 있다.

지원되는 인코딩 목록

- 'utf8': 인코딩을 지정하지 않았을 때 사용하는 기본 값
- 'utf16le': 리틀 엔디안 순서를 사용하는 2바이트 유니코드 문자
- 'latin1': 문자당 1바이트를 사용하는 ISO-8859-1 인코딩
- 'ascii': 영어로만 이루어진 7비트 ASCII 인코딩
- 'hex': 각 바이트를 ASCII 16진수 숫자 쌍으로 변환
- 'base64': 3바이트 시퀀스를 ASCII 문자 네 개로 변환

> 실제로 이진 데이터를 조작하는 노드 프로그램을 만든다면 버퍼 클래스를 아주 많이 사용하게 될 것이다.

## 이벤트와 이벤트 이미터

복잡한 API 중에는 이벤트 기반 API도 있다. API가 객체 위주로 설계돼썩나, 콜백 함수가 여러 번 호출되어야 하거나, 콜백 함수가 여러 가지 필요한 경우가 이에 해당한다.

예시: net.Server 클래스

이 클래스는 연결을 청므으로 주시하기 시작할 때 listening 이벤트를, 클라이언트가 연결할때마다 connection 이벤트를, 연결이 끊길 때 close 이벤트를 방출한다.

```js
const EventEmitter = require("events");
const net = require("net");
const server = new net.Server();
server instanceof EventEmitter; // true
```

> 자세한 내용은 책 참조.

## 스트림

파일을 복사하는 함수같은 경우 다음과 같이 작성하게 되면 문제가 생길 수 있다.

```js
// 비동기적이지만 스트림을 사용하지 않는(그래서 비효율적인) 함수
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

> 복사할 파일이 아주 크거나 동시에 여러 파일을 복사한다면 실패할 수 있다.(메모리 문제) 또한 파일을 다 읽기 전에는 사본 파일의 기록을 시작할 수 없다.

이러한 문제의 해결책은 **스트리밍 알고리즘**이다. 이 알고리즘은 데이터를 항상 작은 덩어리로 처리하며 데이터 전체를 한 번에 메모리에 담지 않는다는 것이다. 스트리밍 솔루션은 항상 메모리 효율적인 데다 속도도 더 빠를 것이다.

노드의 4가지 스트리밍 타입

- 리더블(데이터 소스)
- 라이터블(데이터가 향하는 대상)
- 듀플렉스(리더블 스티림과 라이터블 스트림을 객체 하나에 조합)
- 트랜스폼(듀플렉스 슽릠과의 차이로, 트랜스폼 스트림에 출력된 데이터는 같은 스트림에서 읽을 수 있다.)

이어지는 아래 내용은 노드의 스트림 클래스에서 데이터를 읽고 쓰는 방법을 설명하기 위해 다음의 내용들을 다룬다.

- 파이프(데이터를 다른 스트림에 쓰는 단순한 작업)
- 비동기 순회(리더블 스트림은 비동기 이터레이터이므로 async 함수 안에서 for/await 루프와 함께 사용하여 순회)
- 스트림 기록과 배압 처리(write 메서드를 설명하며 boolean 반환 값(false)에 따른 배압(경고) 설명)
- 이벤트로 스트림 읽기
  - 플로모드: 읽을 수 있는 데이터가 도착하는 즉시 data 이벤트 형태로 방사
  - 일시정지모드: 스트림은 기본적으로 이 모드로 시작하며, read() 메서드를 직접 호출해 스트림에서 데이터를 가져와야함.

## 프로세스, CPU, 운영 체제 세부 사항

알아 두어야 할 중요한 프로퍼티와 함수들

```js
process.argv; // 명령행 인자 배열
process.arch; // x64 같은 CPU 아키텍처
process.cwd(); // 현재 작업 디렉터리
process.chdir(); // 현재 작업 디렉터리를 변경
process.cpuUsage(); // CPU 사용 현황을 보고
process.env; // 환경 변수 객체
process.execPath; // 노드 실행 파일의 파일시스템 절대 경로
process.exit(); // 프로그램 종료
process.exitCode; // 프로그램 종료할 때 보고할 정수 코드
process.getuid(); // 현재 사용자의 유닉스 사용자 ID
process.hrtime.bigint(); // 정밀한 나노초 타임스탬프
process.kill(); // 다른 프로세스에 신호를 보냄
process.memoryUsage(); // 메모리 사용현황을 객체로 반환
process.nextTick(); // setImmediate()와 마찬가지로 함수를 곧 호출
process.pid; // 현재 프로세스의 프로세스 ID
process.ppid; // 부모 프로세스의 ID
process.platform; // 운영 체제. linux, darwin, win32등
process.resourceUsage(); // 자원 사용 현황을 객체로 반환
process.setuid(); // ID나 이름으로 현재 사용자를 변경
process.title; // ps 리스트에 나타나는 프로세스 이름
process.umask(); // 새로운 파일에 대한 기본 퍼미션을 설정하거나 반환
process.uptime(); // 노드 실행 시간을 초 단위로 반환
process.version; // 노드 버전 문자열
process.versions; // 노드가 사용하는 라이브러리의 버전 문자열
```
