# 자바스크립트 완벽 가이드 13주차 학습 - 혜연

## 16장 노드와 서버 사이드 자바스크립트

### 16.7 파일 작업

- 노드의 fs 모듈은 파일과 디렉터리를 다루는 종합적인 API
- path 모듈은 파일과 디렉터리 이름을 다루는 유틸리티 함수를 정의해 fs 모듈을 보조
- 대부분의 함수가 fs.readFile()과 같이 비차단적이며 콜백 기반이고 비동기적이지만 fs.readFileSync()과 같은 동기적이고 차단적인 변형이 존재
  - 노드10 이후 fs.promises.readFile()과 같은 프라미스 기반 비동기 변형이 추가됨.

#### 16.7.1 경로, 파일 서술자, 파일 핸들

- fs 모듈의 함수로 파일 작업을 하려면 먼저 작업할 파일의 경로를 지정해야 함.
- fs 모듈의 함수 일부는 파일 이름 대신 파일을 열 때 운영 체제 레벨의 참조로 사용하는 정수인 파일 서술자를 받음.

#### 16.7.2 파일 읽기

- 노드에서 파일 콘텐츠를 읽는 방법
  - 파일 콘텐츠를 한 번에 읽기: 파일이 작거나 메모리/성능 문제가 없을 때
  - 스트림을 통해 읽기: 파일 콘텐츠를 연속적으로 처리할 수 있고, 파일 콘텐츠 전체를 메모리에 한 번에 담을 필요가 없을 때
  - 저수준 API를 이용해 읽기: 파일의 정확히 어느 부분을 언제 읽을지 저수준에서 제어해야 할 때

#### 16.7.3 파일 쓰기

- 노드에서 존재하지 않는 파일 이름을 사용해 파일을 쓰면 새로운 파일이 자동으로 만들어짐.
- 파일 쓰기도 파일 읽기와 같이 한 번에 전체 내용을 기록하거나 스트림을 통하거나 저수준에서 파일 서술자를 사용하여 기록할 수 있음.

#### 16.7.4 기타 파일 작업

- fs 모듈의 자체 메서드인 fs.copyFile(), fs.copyFileSync(), fs.promises.copyFile()을 이용하여 파일의 사본을 생성할 수 있음.
- fs.rename(), fs.renameSync(), fs.rename() 함수로 파일을 이동시키거나 이름을 바꿀 수 있음.
- fs.unlink(), fs.unlinkSync(), fs.promises.unlink() 함수로 파일을 삭제할 수 있음.

#### 16.7.5 파일 메타데이터

- fs.stat(), fs.statSync(), fs.promises.stat() 함수는 지정된 파일이나 디렉터리의 메타데이터를 반환
- fs.lstat()과 그 변형들은 지정된 파일이 심볼릭 링크인 경우 링크 자체의 메타데이터를 반환
- fs.chmod(), fs.lchmod(), fs.fchmod()와 그 변형들은 파일이나 디렉터리의 모드(퍼미션)를 변경
- fs.chown(), fs.lchown(), fs.fchown()와 그 변형들은 파일이나 디렉터리의 소유자와 그룹 ID를 변경
  - fs.chmod()로 변경한 퍼미션과 상호 작용하므로 함께 변경
- fs.utimes(), fs.futimes()와 그 변형들은 파일이나 디렉터리의 접근 시간과 변경 시간을 변경

#### 16.7.6 디렉터리 작업

- 노드에서 새로운 디렉터리를 생성할 때 fs.mkdir(), fs.mkdirSync(), fs.promises.mkdir()을 사용
- fs.mkdtemp()와 그 변형들은 경로 분자열을 받아서 보안을 위해 임의의 문자 몇 개를 추가하고 그 이름으로 디렉터리를 생성한 후 디렉터리 경로를 반환하거나 콜백에 전달
- fs.rmdir()과 그 변형들은 비어 있는 디렉터리를 삭제
- fs.readdir()과 그 변형들은 디렉터리 전체를 한 번에 읽고 문자열 배열 또는 각 아이템의 이름과 타입을 가진 dirent 객체의 배열을 반환하여 디렉터리 콘텐츠를 열거
- fs.opendir()과 그 변형들은 스트림을 사용하여 지정된 디렉터리를 나타내는 Dir 객체를 반환하므로 디렉터리에 파일이 수천개 있어야 하는 경우 적합

### 16.8 HTTP 클라리언트와 서버

- 노드의 http, https, http2 모듈은 HTTP 프로토콜의 모든 기능은 갖추었으나 비교적 저수준으로 구현됨.
- http와 https 모듈을 써서 HTTP, HTTPS 요청에 응답하는 서버를 만드는 방법
  1. 새로운 서버 객체를 생성
  2. listen() 메서드를 호출해 지정된 포트로 들어오는 요청을 주시
  3. request 이벤트의 이벤트 핸들러를 등록하고, 이 핸들러를 통해 클라이언트의 요청을 읽고 이에 응답하는 프로그램을 작성

### 16.9 HTTP를 사용하지 않는 네트워크 서버와 클라이언트

- 노드는 HTTP를 사용하지 않는 네트워크 서버와 클라이언트도 지원할 수 있음.

### 16.10 자식 프로세스

- 노드로 다른 프로그램을 실행하는 스크립트도 만들 수 있음.
- 노드의 child_process 모듈은 다른 프로그램을 자식 프로세스로 실행할 때 필요한 함수를 갖추고 있음.
- execSync()와 execFileSync()는 다른 프로그램을 실행하는 가장 쉬운 방법으로, 동기적이므로 자식 프로세스가 종료되기 전까지 종료되지 않으며 다른 프로세스를 차단함.
- exec(), execFile()은 오류 우선 콜백을 마지막 인자로 받고 실행 중인 자식 프로세스를 나타내는 ChildProcess 객체를 반환한 즉시 종료
- spawn() 함수는 자식 프로세스가 실행 중일 때도 그 출력에 스트림으로 접근하거나 자식 프로세스에 데이터를 보내는 등 자식 프로세스와 동적으로 상호작용할 수 있음.
- fork() 함수는 자식인 노드 프로세스에서 자바스크립트 코드 모듈을 실행하는 데 특화되어 있음.

### 16.11 워커 스레드

- 노드 10부터 웹 브라우저의 웹 워커 API를 반영한 멀티스레드 프로그래밍 모델을 지원함.
- 자바스크립트 스레드는 노드나 브라우저 모두 기본적으로 메모리를 공유하지 않으므로 자바스크립트 워커는 멀티스레드 프로그래밍의 위험성이나 어려움이 적음.
- 노드 애플리케이션에서 뭐커 스레드를 사용하는 이유
  - 애플리케이션에서 과학 계산, 머신 러닝, 그래픽 처리 등 CPU 코어 하나로 감당하기 어려운 계산을 할 때 스레드를 통해 부하를 코어 여러 개로 분산할 수 있음.
  - 메인 스레드의 반응성을 높일 수 있음.
  - 워커를 사용하면 차단적이고 동기적인 동작이 비차단적이고 비동기적인 작업으로 바뀜.
