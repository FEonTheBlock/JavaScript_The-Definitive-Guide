# 노드와 서버 사이드 자바 스크립트

노드는 운영 체제와 연결된 자바스크립트이며 자바스크립트 프로그램에서 파일을 읽고 쓰고, 자식 프로세스를 실행하고, 네트워크를 통해 통신할 수 있게 한다.
노드의 가장 큰 특징은 비동기적인 API를 바탕으로 한, 싱글 스레드의 이벤트 기반 병렬 환경이다.

## 노드는 기본적으로 비동기적

자바스크립트는 범용 프로그래밍 언어이므로 거대한 행렬 연산이나 복잡한 통계 분석 같은 CPU 집약적인 프로그램도 얼마든지 작성할 수 있다.  
반면 노드는 네트워크 서버처럼 입출력 집약적인 프로그램에 맞게 설계되고 최적화됐다. 특히 노드는 최대한 많은 요청을 동시에 처리할 수있는 서버를 쉽게 만들 수 있도록 설계되었다.

노드는 대부분의 언어와 달리 동시성을 스레드로 구현하지 않는다. 멀티스레드는 정확하게 프로그래밍하기 어렵고, 디버깅하기도 어렵다. 또한 스레드는 무거운 작업이므로 수백 개의 스레드를 사용해 수백 개의 요청을 동시에 처리하려면 엄청난 메모리가 필요하다.

노드는 기본적으로 비동기, 비차단 방식 API를 채택하여 싱글스레드 프로그램이 모델을 유지하면서도 높은 수준의 동시성을 구현한다. 노드는 비차단 방식을 대단히 중요하게 생각하며, 이를 놀라울 정도로 철저히 지킨다.

## 버퍼

노드에서 자주 사용하는 데이터 타입 중에는 버퍼 클래스가 있다. 버퍼 클래스는 파일이나 네트워크에서 데이터를 읽을 때 자주 사용한다. 버퍼는 문자열과 비슷하지만 문자 시퀀스가 아니라 바이트 시퀀스라는 점이 다르다.

## 스트림

데이터를 처리하는 알고리즘을 구현할 때는 데이터 전체를 메모리에 읽어들이고, 이를 처리한 다음 데이터를 내보내는 방식이 항상 가장 쉽다.
스트리밍 알고맂므의 요점은 데이터를 항상 작은 덩어리로 처리하며 절대 데이터 전체를 한 번에 메모리에 담지 않는다는 것이다. 사용할 수만 있다면 스트리밍 솔루션은 항상 메모리 효율적인 데다 속도도 더 빠를 것이다. 노드의 네트워크 API는 스트리밍 기반이고 노드의 파일시스템 모듈 역시 파일을 읽고 쓰는 스트리밍 API를 갖추고 있다.