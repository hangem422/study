# TCP vs UDP

전송계층은 송신자와 수신자를 연결하는 통신서비스를 제공하는 계층으로, 쉽게 말해 데이터의 전달을 담당합니다. 그리고 데이터를 보내기 위해 사용하는 프로토콜이 있는데, 그 프로토콜들이 바로 오늘의 주인공 TCP와 UDP입니다.

## 1. TCP/IP

TCP가 가상 회선 방식을 제공한다는 것은 발신지와 수신지를 연결하여 패킷을 전송하기 위한 **논리적 경로를 배정**한다는 말입니다. 그리고 **3-way handshaking** 과정은 목적지와 수신지를 확실히 하여 정확한 전송을 보장하기 위해서 세션을 수립하는 과정을 의미합니다. TCP가 이러한 특징을 지니는 이유는 간단명료합니다. 대부분의 인터넷 응용 분야들은 신뢰성과 순차적인 전달을 필요로 합니다. UDP로는 이를 만족시킬 수 없으므로 다른 프로토콜이 필요하여 탄생한 것이 TCP입니다.

TCP(Transmission Control Protocol)은 신뢰성이 없는 인터넷을 통해 종단간에 **신뢰성 있는 바이트 스트림을 전송하도록 특별히 설계**되었습니다. 이런 기능 때문에 UDP보다 속도가 느립니다. TCP 서비스는 송신자와 수신자 모두가 소켓이라고 부르는 종단점을 생성함으로서 이루어지고, 연속성 보다는 신뢰성있는 전송이 중요할 때에 사용됩니다. Streaming 서비스에 불리합니다.(손실된 경우 재전송 요청을 하므로)

### 1.1 Connection Oriented

두 개 엔드포인트(로컬, 리모트) 사이에 연결을 먼저 맺고 데이터를 주고받는다. 여기서 TCP 연결 식별자는 두 엔드포인트의 주소를 합친 것으로, <로컬 IP 주소, 로컬 포트 번호, 리모트 IP 주소, 리모트 포트 번호> 형태입니다.

### 1.2 Bidirectional Byte Stream

양방향 데이터 통신을 하고, 바이트 스트림을 사용합니다.

### 1.3 In-order Delivery

송신자(Sender)가 보낸 순서대로 수신자(receiver)가 데이터를 받습니다. 이를 위해서는 데이터의 순서가 필요합니다. 순서를 표시하기 위해 32-bit 정수 자료형을 사용합니다.

### 1.4 Reliability Through ACK

데이터를 송신하고 수신자로부터 (데이터 받았음)을 받지 않으면, 송신자 TCP가 데이터를 재전송합니다. 따라서 송신자 TCP는 송신자로부터 ACK를 받지 않은 데이터를 보관합니다.

### 1.5 Flow Control

송신자는 수신자가 받을 수 있는 만큼 데이터를 전송합니다. 수신자가 자신이 받을 수 있는 바이트 수 (사용하지 않는 버퍼 크기, receive window)를 송신자에게 전달합니다. 송신자는 수신자가 허용하는 바이트 수만큼 데이터를 전송합니다.

### 1.6 Congestion Control

네트워크 정체를 방지하기 위해 receive window와 별도로 congestion window를 사용하는데 이는 네트워크에 유입되는 데이터양을 제한하기 위해서 입니다. Receive window와 마찬가지로 congestion window가 허용하는 바이트 수만큼 데이터를 전송하며 여기에는 TCP Vegas, Westwood, BIC, CUBIC 등 다양한 알고리즘이 있습니다. Flow Control과 달리 송신자가 단독으로 구현합니다.

### 1.7 Full-Duplex, Point to Point

모든 TCP 연결은 전이중(full-duplex), 점대점(point to point) 방식입니다. 전이중이랑 전송이 양방향으로 동시에 일어날 수 있음을 의미하며 점대점이란 각 연결이 정확히 2개의 종단점을 가지고 있음을 의미합니다. TCP는 멀티캐스팅이나 브로드캐스팅을 지원하지 않습니다.

## 2. UDP

UDP(User Datagram Protocol)은 **비연결형 프로토콜**입니다. 즉, 연결을 위해 할당되는 논리적인 경로가 없는데, 그렇기 때문에 각각의 패킷은 다른 경로로 전송되고, **각각의 패킷은 독립적**인 관계를 지니게 됩니다. IP 데이터그램을 캡슐화하여 보내는 방법과 연결 설정을 하지 않고 보내는 방법을 제공합니다. UDP는 흐름제어, 오류제어 또는 손상된 세그먼트의 수신에 대한 재전송을 하지 않습니다. 이 모두가 사용자 프로세스의 몫입니다. UDP가 행하는 것은 포트들을 사용하여 UDP 프로토콜에 인터페이스를 제공하는 것입니다.

종종 클라이언트는 서버로 짧은 요청을 보내고, 짧은 응답을 기대합니다. 만약 요청 또는 응답이 손실된다면, 클라이언트는 time out되고 다시 시도할 수 있으면 됩니다. 코드가 간단할 뿐만 아니라 TCP처럼 **초기설정**(intial setup)에서 요구되는 프로토콜보다 적은 메시지가 요구됩니다. TCP보다 속도가 빠르며 네트워크 부하가 적다는 장점이 있지만 신뢰성있는 데이터의 전송을 보장하지 못합니다. 그렇기 때문에 신뢰성보다는 연속성이 중요한 서비스에서 자주 사용됩니다.

UDP를 사용한 것들에는 DNS가 있습니다. 어떤 호스트 네임의 IP 주소를 찾을 필요가 있는 프로그램은, DNS 서버로 호스트 네임을 포함한 UDP 패킷을 보냅니다. 이 서버는 호스트 IP 주소를 포함한 UDP 패킷으로 응답합니다. 사전에 설정이 필요하지 않으며 그 후에 해제가 필요하지 않습니다.

## 3. 출처

- [Interview_Question_for_Beginner - JaeYeopHan](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Network#http%EC%9D%98-get%EA%B3%BC-post-%EB%B9%84%EA%B5%90)
- [[TCP/UDP] TCP와 UDP의 특징과 차이 - 망나니개발자](https://mangkyu.tistory.com/15)
- [TCP/IP 네트워크 스택 이해하기 - Nave D2](https://d2.naver.com/helloworld/47667)
- [TCP 와 UDP 차이를 자세히 알아보자 - hi, daehyunlee](https://velog.io/@hidaehyunlee/TCP-%EC%99%80-UDP-%EC%9D%98-%EC%B0%A8%EC%9D%B4)
