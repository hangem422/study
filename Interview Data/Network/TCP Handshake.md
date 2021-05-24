# TCP 3-Way Handshake& & 4-Way Handshake

## 1. 연결 성립(Connection Establishment)

1. 클라이언트는 서버에 접속을 요청하는 SYN(a) 패킷을 보냅니다.
2. 서버는 클라이언트의 요청인 SYN(a)를 받고 클라이언트에게 요청을 수락한다는 ACK(a + 1)와 SYN(b)이 설정된 패킷을 발송합니다.
3. 클라이언트는 서버의 수락 응답인 ACK(a + 1)와 SYN(b) 패킷을 받고 ACK(b + 1)를 서버로 보내면 연결이 성립(establish)됩니다.

## 2. 연결 헤제(Connection Termination)

1. 클라이언트가 연결을 종료하겠다는 FIN 플래그를 전송합니다.
2. 서버는 클라이언트의 요청(FIN)을 받고 알겠다는 확인 메세지로 ACK를 보냅니다. 그리고 데이터를 모두 보낼 때까지 잠시 TIME_OUT 됩니다.
3. 데이터를 모두 보내고 통신이 끝났으면 연결이 종료되었다고 클라이언트에게 FIN 플래그를 전송합니다.
4. 클라이언트는 FIN 메세지를 확인했다는 메세지(ACK)를 보냅니다.
5. 클라이언트의 ACK 메세지를 받은 서버는 소켓 연결을 Close 합니다.
6. 클라이언트는 아직 서버로부터 받지 못한 데이터가 있을 것을 대비해 일정 시간 동안 세션을 남겨놓고 잉여 패킷을 기다리는 과정을 거칩니다. (TIME_WAIT)

## 3. 용어 정리

- SYN: synchoriniz sequence number
- ACK: acknowledgement

TCP Header에는 Code Bit(Flag bit)라는 부분이 존재합니다. 이 부분은 총 6Bit로 이루어져 있으며 각각 한 bit들이 의미를 갖고 있습니다. **Urg-Ack-Psh-Rst-Syn-Fin** 순서로 되어 있으며 해당 위치의 비트가 1이면 해당 패킷이 어떠한 내용을 담고 있는 패킷인지를 나타냅니다. SYN 패킷을 경우엔 000010이 되고 ACK 패킷일 경우에는 010000이 됩니다.

#### 왜 패킷이 두종류인가?

일단 연결이 성립되려면 서로 통신이 가능한지를 먼저 파악하기 위해 패킷을 주고받아야 합니다. 두 종류의 패킷을 주고 받는 것은, 요청과 응답에 대한 패킷을 주고 받아야하기 때문입니다.

#### 왜 2-Way가 아닌 3-Way인가?

비유를 들어봅시다. 일단 클라이언트가 자신의 목소리가 들리는지 물어봅니다(SYN). 서버는 클라이언트의 목소리가 들린다고 말합니다(SYN + 1). 그리고 자신의 목소리가 들리는지 물어봅니다(ACK). 클라이언트는 서버의 목소리가 들린다고 말합니(ACK + 1). TCP connection은 양방향성(bidirectional) connection 입니다. 클라이언트에서 서버에게 존재를 알리고 패킷을 보낼 수 있다는 것을 알리듯, 서버에서도 클라이언트에게 존재를 알리고 패킷을 보낼 수 있다는 신호를 보내야 합니다. 그렇기 때문에 2-way-hanshake로는 부족합니다.

#### 왜 Randomized Sequence Number인가?

처음 클라이언트에서 SYN 패킷을 보낼 때 Sequence Number에는 랜덤한 숫자가 담겨집니다. 초기 Sequence Number를 ISN이라고 합니다. ISN이 0부터 시작하지 않고 난수를 생성해서 number를 설정하는 이유는 무엇일까요? Connection을 맺을 때 사용하는 포트(port)는 유한 범위 내에서 사용하고 시간이 지남에 따라 재사용됩니다. 따라서 두 통신 호스트가 과거에 사용된 포트 번호 쌍을 사용하는 가능성이 존재합니다. 서버 측에서 패킷의 SYN을 보고 패킷을 구분하게 되는데 난수가 아닌 순차적인 Number가 전송되면 이전의 connection으로부터 오는 패킷으로 인식할 수 있습니다. 이러한 문제가 발생할 가능성을 줄이기 위해서 난수로 ISN을 설정하는 것입니다.

## 4. 출처

- [[TCP] 3-way-handshake & 4-way-handshake -\_Jbee](https://asfirstalways.tistory.com/356)