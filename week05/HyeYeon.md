# 자바스크립트 완벽 가이드 5주차 학습 - 혜연

## 13장 비동기 자바스크립트

- 비동기적이다: 데이터가 들어오거나 어떤 이벤트가 일어날 때까지 계산을 멈추고 대기하는 일이 잦다
- 웹 브라우저의 자바스크립트 프로그램은 일반적으로 이벤트 주도적임.
  - 프로그램이 실제로 무언가를 실행하기 전에 사용자의 액션을 기다림.

### 13.1 콜백과 비동기 프로그래밍

- 자바스크립트의 가장 기본적인 비동기 프로그래밍은 콜백을 통해 이뤄짐.

#### 13.1.1 타이머

- setTimeout() 함수로 타이머를 설정하여 일정 시간이 지난 후 코드를 실행하는 것도 단순한 비동기 프로그램 유형 중 하나임.
- setTimeout()는 콜백 함수를 한 번 호출
  - 첫 번째 인자는 함수, 두 번째 인자는 시간(밀리초)
- 반복적으로 실행하는 타이머의 경우 setInterval() 함수 사용.
  - setInterval()이 반환하는 값을 clearInterval()에 넘겨 반복 호출을 중단

#### 13.1.2 이벤트

- 이벤트 주도 자바스크립트 프로그램은 지정된 컨텍스트에 지정된 타입의 이벤트를 처리할 콜백 함수(이벤트 핸들러, 이벤트 리스너)를 등록하고, 웹 브라우저는 지정된 이벤트가 일어날 때마다 함수를 호출
- 이벤트 콜백 함수를 addEventListener()를 통해 등록
  - 첫 번째 인자는 주시할 이벤트를 지정하는 문자열('click' 등), 두 번째 인자는 콜백 함수

#### 13.1.3 네트워크 이벤트

- 네트워크 요청도 자바스크립트 프로그래밍의 대표적인 비동기 유형 중 하나
- 클라이언트 사이드 자바스크립트 코드는 XMLHttpRequest 클래스와 콜백 함수를 사용해 HTTP 요청을 보내고 서버의 응답을 비동기적으로 처리
- 최신 클라이언트 사이드 자바스크립트에서는 XMLHttpRequest 클래스 대신 fetch() API를 사용
- 같은 객체, 같은 이벤트 타입에 다른 리스너를 추가로 등록하지 않을 경우 addEventListener()를 호출하기보다 적절한 콜백 함수를 프로퍼티로 할당하는 것이 좋음.

#### 13.1.4 노드의 콜백과 이벤트

- 노드는 비동기적으로 만들어져 있으며 많은 API가 콜백과 이벤트를 사용함.
- 노드는 addEventListener() 대신 on() 메서드를 사용해 이벤트 리스너를 등록함.

### 13.2 프라미스

- 프라미스: 비동기 작업의 결과를 나타내는 객체
- 콜백 기반 비동기 프로그래밍의 문제점
  1. 콜백 헬로 가독성이 떨어짐.
     - 프라미스는 콜백 중첩을 프라미스 체인으로 바꾸어 가독성을 높임.
  2. 에러 처리가 어려움.
     - 비동기 작업에서 예외가 일어나면 호출자가 스택에 존재하지 않으므로 호출자에게 예외를 전달하기 쉽지 않음.
     - 프라미스는 에러 처리 방법을 표준화함.
- 프라미스는 setInterval, click 이벤트와 같이 콜백 함수를 반복적으로 호출하는 상황에 사용할 수는 없음.

#### 13.2.1 프라미스 사용

- 프라미스 객체에는 then() 인스턴스 메서드가 있고, 콜백 함수를 then() 메서드에 전달함.
- 프라미스 객체에서 then() 메서드를 여러 번 호출하면 각 콜백은 비동기 작업이 완료될 때 호출됨.
- 이벤트 리스너와 달리 프라미스는 한 가지 작업이며 then()에 등록된 각 함수는 단 한 번만 호출됨.
- 프라미스를 반환하는 함수, 프라미스 결과를 사용하는 함수 모두 이름을 동사 형태로 짓는 관행이 있음.
- 프라미스의 에러 처리
  - 프라미스 객체가 반환된 후에 동작이 이루어지므로 동작이 값을 반환할지, 예외를 일으킬지 미리 알 수 없음.
  - then() 메서드에 두 번째 인자로 함수를 전달해 에러를 처리할 수 있음.
  - 프라미스에서 정상적으로 작업이 완료되면 then()의 첫 번째 인자인 함수에 결과를 전달
  - 프라미스에서 예외가 발생하면 then()의 두 번째 인자인 함수에 예외(일반적으로 Error 객체)를 전달
  - catch()를 이용하여 에러 처리 코드를 만드는 경우가 더 흔함.

#### 13.2.2 프라미스 체인

- 비동기 작업 시퀀스를 then()의 체인으로 이어서 콜백 헬을 방지한다는 점이 프라미스의 가장 중요한 장점 중 하나임.
- 프라미스를 콜백처럼 중첩해 사용ㅎ아는 것은 프라미스의 설계 목적에 부합하지 않으므로 프라미스 체인을 사용하는 것이 좋음.
- then() 메서드를 호출할 때마다 새 프라미스 객체를 반환하며, 새 프라미스 객체는 then()에 전달된 함수가 완료되기 전에는 이행되지 않음.

#### 13.2.3 프라미스 해석

- 프라미스가 해석된 상태라는 말은 프라미스가 다른 프라미스와 연결됐다는 의미
- 콜백 c를 then() 메서드에 전달하면 then()은 프라미스 p를 반환하고 나중에 c를 비동기적으로 호출할 수 있도록 준비함.
- 콜백은 작업을 마치면 값 v를 반환함.
- 콜백이 완료되면 p는 값 v로 해석됨.
  - 프라미스가 프라미스가 아닌 값으로 해석되면 그 프라미스는 그 값으로 즉시 이행되므로 c가 프라미스가 아닌 값을 반환하면 그 반환 값은 p의 값이 되고, p가 이행되면서 끝남.
- 반환값 v가 프라미스라면 p는 해석되기는 했지만 이행되지는 않은 상태임. 이 시점에서 p는 프라미스 v가 완료되기 전에는 완료될 수 없고, v가 이행되면 p는 같은 값으로 이행되며 v가 거부되면 p는 같은 이유로 거부됨.

#### 13.2.4 프라미스와 에러

- 프라미스가 거부됐을 때 두 번째 콜백 함수에 전달되는 인자는 거부 사유를 나타내는 값이며, 일반적으로 Error 객체임.
- 프라미스 관련 에러는 일반적으로 프라미스 체인에 .catch() 메서드를 추가하는 식으로 처리함.
- catch와 finally 메서드
  - 동기적 코드에서 예외가 catch 블록을 만날 때까지 콜 스택을 따라 올라가는 반면, 프라미스의 비동기 체인에서는 .catch()를 만날 때까지 에러가 체인을 따라 내려감.
  - 프라미스 체인에 .finally()를 추가하면 호출한 프라미스가 완료될 때 .finally()가 호출됨.
    - .finally()는 프라미스가 이행되거나 거부될 때 호출되며 아무 인자도 받지 않으므로 콜백 안에서 프라미스의 이행 여부는 알 수 없으나, 이행 여부와 관계 없는 정리 작업이 필요할 때 사용
    - .finally() 콜백의 반환 값은 일반적으로 무시됨.

#### 13.2.5 병렬 프라미스

- Promise.all()
  - Promise.all()을 이용하여 여러 개의 비동기 작업을 병렬로 실행해야 할 수 있음.
  - Promise.all()은 프라미스 객체의 배열을 받고 프라미스를 반환함.
  - 입력 프라미스 중 하나라도 거부되면 반환된 프라미스 역시 거부됨.
    - 첫 번째로 거부되는 프라미스가 생기는 즉시, 나머지 프라미스가 아직 대기 중이더라도 거부됨.
  - 입력 프라미스가 모두 이행되면 전체 프라미스는 각 입력 프라미스 값으로 이루어진 배열로 이행됨.
  - 배열 요소 중 일부가 프라미스가 아니라면 그 값은 이미 이행된 것으로 간주하고 결과 배열에 그대로 복사함.
- Promise.allSettled()
  - ES2020에서 도입
  - 반환된 프라미스를 절대 거부하지 않으며 입력 프라미스 전체가 완료되기 전에는 이행되지 않음.
  - 각각의 반환된 객체에는 status 프로퍼티가 있고 그 값은 fulfilled 또는 rejected
    - fulfilled 상태인 객체에는 value 프로퍼티가 존재하며 그 값은 이행된 값
    - rejected 상태인 객체에는 reason 프로퍼티가 존재하며 그 값은 대응하는 프라미스의 에러 또는 거부 값
- Promise.race()
  - 첫 번째로 이행되는 프라미스의 값만 필요할 때 사용
  - 입력 배열에서 처음으로 이행/거부되는 프라미스와 함께 이행/거부되는 프라미스를 반환

#### 13.2.6 프라미스 생성

- 프라미스 기반 API를 직접 만들 수 있음.
- 다른 프라미스에 기반한 프라미스
  - 프라미스가 있다면 언제든 .then()을 호출해 프라미스를 만들고 반환할 수 있음.
- 동기적인 값을 기반으로 하는 프라미스
  - 함수의 작업 자체에는 비동기 작업이 없지만 프라미스를 반환하게 해야 할 때 Promise.resolve()와 Promise.reject()를 사용
  - Promise.resolve()는 인자 하나만 받고 비동기적으로 즉시 그 값으로 이행되는 프라미스를 반환
  - Promise.reject()는 인자 하나만 받고 그 이유로 거부되는 프라미스를 반환
- 처음부터 만드는 프라미스
  - Promise() 생성자를 사용해서 완전히 제어할 수 있는 새 프라미스 객체를 생성할 수 있음.
  - Promise() 생성자를 호출하면서 인자로 전달하는 함수에는 resolve, reject라는 이름의 두 매개변수를 전달함.

#### 13.2.7 프라미스 시퀀스

- 배열 길이와 콘텐츠가 정해져 있지 않아 프라미스 체인을 미리 만들 수 없을 때 반환과 동시에 이행되는 프라미스를 생성하여 동적으로 만들 수 있음.
- 프라미스를 미리 생성하지 않고 프라미스의 콜백이 다음 프라미스를 생성해 반환하게 하여 동적으로 생성할 수도 있음.