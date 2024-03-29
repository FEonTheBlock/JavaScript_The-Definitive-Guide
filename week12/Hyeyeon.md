# 자바스크립트 완벽 가이드 12주차 학습 - 혜연

## 16장 노드와 서버 사이드 자바스크립트

- 노드의 특징: 비동기적인 API를 바탕으로 한 싱글스레드의 이벤트 기반 병렬 환경
- 노드의 용도
  - 유닉스 셸의 복잡한 문법에서 해방된 최신 셸 스크립트
  - 신뢰할 수 있는 프로그램을 실행하는 범용 프로그래밍 언어
  - 효율적이고 병렬화된 웹 서버 환경

### 16.1 노드 프로그래밍 기본

#### 16.1.1 콘솔 출력

- 표준 출력을 사용하는 가장 쉽고 공식적인 방법은 console.log()
- console.error()는 메시지를 표준 에러 스트림에 기록함.
- 프로그램에서 표준 출력을 파일이나 파이프에 리다이렉트하면 console.log()의 텍스트는 사용자에게 보이지 않고 console.error()의 텍스트만 콘솔에 출력됨.

#### 16.1.2 명령행 인자와 환경 변수

- 노드는 유닉스와 비슷하게 명령행 인자와 환경 변수를 통해 입력을 받음.
- 노드는 process.argv에서 명령행 인자를 읽을 수 있음.
  - 배열 첫 번째 요소는 항상 파일의 경로
  - 배열 두 번째 요소는 노드가 실행하고 있는 자바스크립트 코드 파일의 경로
  - 배열 나머지 요소는 노드를 호출할 때 명령행에 쓴 인자를 공백으로 구분한 결과
  - 파일 이름 다음에 있는 인자는 모두 process.argv에 포함됨.
- 노드는 process.env 객체에 포함된 유닉스 스타일 환경 변수에서도 입력받을 수 있음.
  - process.env 객체의 프로퍼티 이름은 환경 변수 이름이고 프로퍼티 값은 문자열인 변수 값

#### 16.1.3 프로그램 수명

- 노드 프로그램은 초기 파일을 실행하고 콜백을 모두 호출한 뒤 남은 이벤트가 없을 때까지 종료되지 않음.
  - 네트워크 연결을 주시하는 노드 기반 서버 프로그램은 항상 이벤트를 기다리고 있으므로 이론적으로 영원히 실행됨.
- process.exit()를 호출해 프로그램을 강제로 종료할 수 있음.
- 프로그램에서 예외가 일어났는데 이를 캐치할 catch 절이 없으면 프로그램은 스택 추적을 출력하고 종료됨.

#### 16.1.4 노드 모듈

- 노드의 모듈 시스템은 require() 함수로 모듈에 값을 가져오고, export 객체나 module.exports 프로퍼티로 값을 내보냄.
- 노드 13부터는 ES6 모듈 표준도 추가로 지원
- 파일에 .mjs나 .cjs 확장자를 쓰지 않으면 노드는 파일과 같은 디렉터리, 부모 디렉터리에서 가장 가까운 package.json 파일을 찾아 JSON 객체에서 최상위 type 프로퍼티를 체크함.
  - type 프로퍼티가 없으면 기본적으로 파일을 CommomJS로 간주함.

#### 16.1.5 노드 패키지 매니저

- npm(Node Package Manager)는 프로그램에 필요한 라이브러리를 내려받고 관리하는 일을 도움.

### 16.2 노드는 기본적으로 비동기적입니다

- 노드는 네트워크 서버처럼 입출력 집약적인 프로그램에 맞게 설계되고 최적화됨.
- 노드는 비동기, 비차단 방식 API를 채택하여 싱글스레드 프로그래밍 모델을 유지하면서도 높은 수준의 동시성을 구현함.
- 로컬 파일시스템의 파일을 읽고 쓰는 것에도 비차단, 비동기 함수를 지원
- 노드는 다른 프로세스를 차단하는 동기적 버전도 정의했으며, 파일시스템 모듈에 이러한 함수가 많음.
  - 이러한 함수는 일반적으로 이름 뒤에 Sync가 붙음.

### 16.3 버퍼

- Buffer 클래스: 문자열과 비슷하나 문자 시퀀스가 아니라 바이트 시퀀스이며, 파일이나 네트워크에서 데이터를 읽을 때 자주 사용함.
- 텍스트 인코딩 이름을 지정한다면 버퍼 객체를 사용할 필요가 없음.

### 16.4 이벤트와 이벤트이미터

- 노드에서 이벤트를 방사하는 객체는 이벤트이미터(EventEmitter) 또는 그 서브클래스의 인스턴스
- 이벤트이미터의 핵심 기능은 on() 메서드를 통해 이벤트 핸들러 함수를 등록하는 것
- 이벤트 이미터 클래스 중 emit() 메서드로 등록된 이벤트 핸들러 함수를 호출
- 이벤트 핸들러 함수가 반환하는 값은 모두 무시되나 이벤트 핸들러 함수가 일으킨 예외는 emit() 호출의 외부로 전달되어 해당 핸들러 함수 다음으로 등록된 핸들러 함수의 실행을 막음.

### 16.5 스트림

- 스트리밍 알고리즘: 데이터를 항상 작은 덩어리로 처리하며 절대 데이터 전체를 한 번에 메모리에 담지 않는 것
- 스트리밍 솔루션은 메모리 효울적이고 속도도 빠름.
- 노드가 지원하는 네 가지 기본 스트리밍 타입
  - 리더블: 데이터 소스
  - 라이터블: 데이터가 향하는 대상
  - 듀플렉스: 리더블 스트림과 라이터블 스트림을 객체 하나에 조합
  - 트랜스폼: 읽고 쓸 수 있는 스트림으로, 트랜스폼 스트림에 출력된 데이터는 같은 스트림에서 읽을 수 있다는 점에서 듀플렉스와 차이

### 16.6 프로세스, CPU, 운영 체제 세부 사항

- 전역 객체 Process는 현재 실행 중인 노드 프로세스의 상태에 관련된 유용한 프로퍼티와 함수 제공
- os 모듈은 process와 달리 require()로 직접 불러와야 하며 노드가 실행되고 있는 컴퓨터와 운영 체제에 관한 세부 사항을 제공
