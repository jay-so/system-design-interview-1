# 2️⃣ 사용자 수에 따른 규모 확장성 - Part2

---

## 🤨 메시지 큐

---

- 메시지 큐는 메시지의 무손식, 즉 메시지 큐에 일단 보관된 메시지는 소비자가 꺼낼때까지 안전히 보관된다는 특징을 가진 비동기 통신을 지원하는 기능입니다.
- 메시지의 버퍼 역할을 하며, 비동기적으로 전송합니다.

**메시지 큐의 구조**

- 발행자(publisher)라는 입력 서비스가 메시지를 만들어서 메시지 큐에 발행(publish)합니다.
- 큐에는 보통 구독자(subscriber)라는 서비스 혹은 서버가 연결되어 있는데, 메시지를 받아 그에 맞는 동작을 수행합니다.

![17.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F17.png)

**메시지 큐의 장점**

- 메시지 큐를 사용하면 서비스 또는 서버 간의 결합이 느슨해져, 규모의 확장성이 보장되어야 하는 안정적인 어플리케이션을 구성하기에 좋습니다.
- 생산자는 소비자 프로세스가 다운되어 있어도 메시지를 발행할 수 있으며, 소비자는 생산자 서비스가 가용한 상태가 아니더라도 메시지를 수신할 수 있습니다.

**예시**

- 사진 보정 어플리케이션에서 보정은 시간이 오래 걸리는 과정이므로 비동기로 처리하면 편합니다.
- 사진 보정 작업은 해당 작업을 메시지 큐에서 꺼내 비동기적으로 완료합니다.
- 이렇게 하면 생산자와 소비자 서비스의 규모는 각기 독립적으로 확장될 수 있습니다.
- 큐의 크기가 커지면 많은 작업 프로세스를 추가하여 처리 시간을 줄일 수 있으며, 비어 있는 상태라면 작업 프로세스를 줄이는 등의 유동적인 흐름을 처리할 수 있습니다.

![18.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F18.png)

<br/>

## 로그, 메트릭 그리고 자동화

---

- 몇 개의 서버에서 실행되는 소규모 웹 사이트를 만들 때는 로그나, 메트릭, 자동화 같은 것을 하면 좋지만 꼭 할 필요는 없습니다.
- 하지만 웹 사이트가 확장되면 해당 부분을 도입하는 것을 권장합니다.

**로그**

- 에러 로그를 모니터링하는 것은 중요합니다.
- 시스템의 오류와 문제들을 보다 쉽게 찾아낼 수 있으며, 여러 서버에서의 로그를 모아주는 단일 서비스의 로그 도구를 이용하면 더 편리하게 검색하고 조회할 수 있습니다.

**메트릭**

- 메트릭을 잘 수집하면 사업 현황에 필요한 정보를 얻을 수 있으며, 현재 시스템의 상태를 쉽게 파악할 수 있습니다.
- 호스트 단위 메트릭
    - CPU, 메모리, 디스크 I/O에 관한 메트릭이 해당 부분에 속합니다.
- 종합 단위 메트릭
    - 데이터베이스 계층의 성능, 캐시 계층의 성능이 해당 부분에 속합니다.
- 핵심 비즈니스 메트릭
    - 일별 사용자, 수익, 재방문 부분이 해당 부분에 속합니다.

**자동화**

- CI/CD 툴을 이용하여, 개발자가 만드는 코드가 어떤 검증 절차를 자동으로 거쳐 문제를 쉽게 감지할 수 있습니다.
- 이외에도 빌드 및 테스트, 배포 등의 절차를 자동화할 수 있어 개발 생산성을 크게 향상시킬 수 있습니다.

### 👉🏻 메시지 큐, 로그, 메트릭, 자동화 등을 반영하여 수정한 설계안

- 해당 과정은 메시지 큐를 적용하여 수정한 예시입니다.
- 메시지 큐는 각 컴포넌트가 보다 느슨한 결합이 될 수 있도록 하고, 결합에 대한 내성을 높입니다.
- 로그, 모니터링, 메트릭, 자동화 등을 지원하기 위한 장치를 추가하였습니다.

![19.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F19.png)

<br/>

## 🫡 데이터베이스의 규모 확장

---

- 저장할 데이터가 많아지면, 데이터베이스에 대한 부하도 증가합니다.
- 이때에는 데이터베이스를 증설할 방법을 찾아야 하며, 하나는 수직적 규모 확장법이며 하나는 수평적 규모 확장법입니다.

### 👉🏻 수직적 확장

- 기존 서버에 더 많은 성능을 확장하므로 처리하는 방법입니다.
- 하나의 데이터베이스에 더 많은 데이터를 저장할 수 있으나 다음과 같은 문제가 있습니다.

**문제점**

- 데이터베이스 서버 하드웨어는 한계가 있으므로, CPU 및 RAM 등을 무한 증설할 수 없습니다.
- SPOF로 인한 위험성이 큽니다.
- 비용이 많이 들게 됩니다.

### 👉🏻 수평적 확장

- 데이터 베이스의 수평적 확장은 샤딩이라고 부르며, 더 많은 서버를 추가함으로 성능을 향상 시키는 방법입니다.

**수직적 확장법과 수평적 확장의 비교**

![20.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F20.png)

- 샤딩은 대규모 데이터를 샤드라고 부르는 작은 단위로 분할합니다.
- 모든 샤드는 같은 스키마를 사용하지만, 샤드에 보관되는 데이터 사이에는 중복이 없습니다.

**예시**

- 사용자 데이터를 어느 샤드에 넣을지는 사용자 ID에 따라서 정해집니다.
- **`user_id % 4`** 함수를 통해 데이터를 저장하는 방법

![21.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F21.png)

- 각 샤드 노드에는 사용자 데이터가 어떻게 되는지 보여주는 예시입니다.

![22.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F22.png)

- 샤딩 전략을 구현할 때 고려해야 가장 중요한 것은 샤딩 키를 어떻게 정의하느냐에 대한 것입니다.
- 샤딩 키는 파티션 키라고도 부르며, 데이터가 어떻게 분산될지 정하는 하나 이상의 칼럼으로 구성됩니다.
- 샤딩 키를 통해 올바른 데이터베이스에 질의를 보내어 데이터 조회나 변경을 처리함으로 효율을 높일 수 있습니다.
- 샤딩 키를 정할 때는 데이터를 고르게 분할 할 수 있도록 하는 것이 가장 중요합니다.

**샤딩을 도입하면서 발생되는 새로운 문제**

**데이터의 재 샤딩**

- 데이터가 너무 많아져서 하나의 샤드로는 더 이상 감당하기 어려울 때
- 샤드 간 데이터 분포가 균등하지 못해 어떤 샤드에 할당된 공간 소모가 다른 샤드에 비해 빨리 진행될 때, 해당 현상을 샤드 소진이라고 합니다.
- 해당 현상이 발생되면 샤드 키를 계산하는 함수를 변경하고 데이터를 재배치해야 합니다.

유명 인사 문제

- 핫 스팟 키 문제라고도 불리는데, 특정 샤드에 질의가 집중되어 과부화되는 문제입니다.
- 자주 되는 데이터가 있을 때, 해당 데이터를 자주 읽어옴으로써 해당 샤드에 부하가 걸리는 문제입니다.
- 이때는 각각의 샤드에 나누어서 재배치할 수 있으며, 더 잘게 쪼개서 사용할 수 있습니다.

**조인과 비정규화**

- 하나의 데이터베이스를 여러 샤드 서버로 쪼개면, 여러 샤드에 걸친 데이터를 조인하기 힘들어집니다.
- 이를 해결하기 위해 비정규화하여 하나의 테이블에서 질의가 수행될 수 있도록 합니다.

**데이터 베이스 샤딩을 적용한 아키텍처**

![23.png](..%2F..%2F..%2Fimages%2Fjay-so%2F1%EC%9E%A5%2F23.png)

<br/>

## 🤗 백만 사용자, 그리고 그 이상

---

**시스템의 규모를 확장을 위해 고려해봐야 할점**

- 웹 계층은 무상태 계층으로 도입할지의 과정
- 모든 계층에 다중화를 도입하는 과정
- 가능한 한 많은 데이터를 캐시할 것
- 여러 데이터 센터를 지원할 것
- 정적 콘텐츠는 CDN을 통해 서비스 할 것
- 데이터 계층은 샤딩을 통해 해당 규모를 확장할 것
- 각 계층은 독립적 서비스로 분할할 것
- 시스템을 지속적으로 모니터링하고, 자동화 도구들을 활용할 것

<br/>

### 📖 Reference

---

[가상 면접 사례로 배우는 대규모 시스템 설계 기초](https://m.yes24.com/Goods/Detail/102819435)