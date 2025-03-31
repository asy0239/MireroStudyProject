## RabbitMQ??(https://www.rabbitmq.com/)

RabbitMQ 는 다양한 소프트웨어 애플리케이션 간의 통신을 가능하게 하는 메세지 브로커 소프트웨어이다. RabbitMQ 는 AMQP(Advanced Message Queuing Protocol)를 기반으로 하며 최신 분산 시스템을 위한 안정적이고 확장 가능한 메시징 솔루션을 제공.

다양한 애플리케이션과 서비스 간의 비동기 통신을 허용한다. 확장성이 뛰어나 다양한 프로그래밍 언어 및 프레임워크와 통합될 수 있다.

## RabbitMQ 아키텍처

RabbitMQ 는 서버가 메시지 저장 및 전달을 담당하고 클라이언트가 메세지 생성 및 소비를 담당하는 client-server 모델을 사용. 이는 메시지가 대기열에 배치된 다음 클라이언트에서 사용되는 대기열 기반 모델을 사용

RabbitMQ 는 AMQP, MQTT, STOMP 와 같은 여러 메세징 프로토콜을 지원. 또한 메시지 라우팅, 메시지 확인, 메시지 지속성과 같은 고급 기능도 지원

## Publishing 및 Consuming

RabbitMQ 를 설정하고 나면 메시지 게시(Publishing) 및 소비(Consuming)를 시작할 수 있다. 메시지를 게시하려면 RabbitMQ 에 대한 연결을 만든 다음 채널을 만들어야 함. 그런 다음 채널을 사용하여 메시지를 대기열에 게시할 수 있음.

메시지를 소비하려면 대기열을 수신하고 메시지를 처리하는 소비자(Consumer)를 생성해야 한다.

## 주요 개념 및 용어 정리

### Producer

메시지를 생성하고 발송하는 주체(어플리케이션)
Queue 에 직접 접근하지 않고 Exchange 를 통해 접근

### Consumer

메시지를 수신하는 주체(유저 어플리케이션)
Queue에 직접 접근하여 메시지를 가져온다.
동일 업무를 처리하는 Consumer 는 보통 하나의 Queue 를 바라본다.
동일 업무를 처리하는 Consumer 가 여러 개인 경우 같은 Queue 를 바라보면 자동으로 메시지를 분배하여 전달한다.

### Queue

메시지를 저장하는 버퍼
Queue 는 Exchange 에 Binding 된다.
이름으로 구분된다.
같은 이름, 같은 설정으로 Queue 를 생성하면 에러 없이 연결된다.
같은 이름, 다른 설정으로 Queue 를 생성하면 에러가 발생한다.

### Binding

Exchange 에게 메시지를 라우팅 할 규칙을 지정한다(Exchange 와 Queue를 연결한다는 뜻)
Exchange 와 Queue 는 m:n Binding 이 가능하다.

### Exchange

Producer 가 전달한 메시지를 어떤 Queue 에 발송할지 결정한다.
Exchange 의 4가지 타입에 따라 동작하며 일종의 라우터 개념이다.

- Direct : RoutingKey 가 정확히 일치하는 Queue 에 메시지 전송(Unicast)
- Topic : RoutingKey 패턴이 일치하는 Queue 에 메시지 전송(Multicast)
- Headers : [Key:Value] 로 이루어진 header 값을 기준으로 일치하는 Queue 에 메시지 전송, 모든 Header 가 일치할 때, 하나라도 일치할 때 메시지를 전송 등의 옵션 설정 가능(Multicast)
- Fanout : 해당 Exchange 에 등록된 모든 Queue 에 메시지 전송(Broadcase)

### Pubilsh / Subscribe

Publish 는 메시지를 발송하는 것을 의미한다.
Subscribe 는 Consumer 가 메시지를 수신하기 위해 Queue 를 실시간으로 리스닝 하는 것을 의미한다.

### Routing / RoutingKey

Routing 은 Exchange 가 Queue 에 메시지를 전달하는 과정을 의미한다.
RoutingKey 는 Exchange 가 Queue 에 메시지를 전달할지 결정하는 기준이다.

# Exchange

![image](https://www.cloudamqp.com/img/blog/exchanges-topic-fanout-direct.png)

## Direct Exchange

RoutingKey 를 이용하여 메시지를 Routing 한다. 하나의 Queue 에 여러 개의 RoutingKey 를 지정할 수 있다.

위 그림을 해석하면 error 메시지는 C1 에 info, error, warning 메시지는 C2 에서 처리한다. 여러 Queue 에 같은 RoutingKey 를 지정하여 Fanout 처럼 동작시킬 수 있다.

RabbitMQ 의 Default Exchange 는 Direct 타입이며 RabbitMQ 에서 생성되는 모든 Queue 가 자동으로 Binding 된다.

## Topic Exchange

RoutingKey 의 패턴을 이용하여 메시지를 라우팅한다.

RoutingKey 가 example.orange.rabbit 인 경우 Q1(Queue1) 에 orange 가 Q2(Queue2) 에 rabbit 이 있어 Q1 과 Q2 모두에 전달된다.

RoutingKey 가 example.orange.turtle 인 경우 Q1 에만 매칭되어 Q1 에만 전달된다.

RoutingKey 가 lazy.grape.rabbit 인 경우 Q2 에 rabbit 과 lazy 가 있어 Q2 에만 전달된다. 매칭되는 패턴이 여러 개 일치하더라도 하나의 Queue 에는 한 번만 전달된다.

## Headers Exchange

Topic Exchange 와 유사하지만 라우팅을 위해 Header 를 쓴다.

그림에서는 Header 의 p[key1:value1] 에 매칭되는 것이 맨 위의 Queue 이므로 맨 위에만 전달된다.

## Fanout Exchange

Exchange 에 등록된 모든 Queue 에 메시지를 전송한다.