# Udp

## Udp를 공부하게 된 계기

회사 프로젝트 개발중에 안드로이드와 PC간의 Udp 연결을 해야할 일이 있었는데 평소 잘 알지 못했던 Socket 통신에 대해 더 알고 싶어서 공부하게 되었다    
~~Tcp도 공부해야하나 고민중...~~

## Udp란?
UDP(User Datagram Protocol)는 TCP/IP 프로토콜의 4계층인 통신계층 (transport Layer)에서 사용되는 통신 규약이다. 통신계층은 송신자와 수신자의 데이터 전달을 담당하는 계층으로 크게 TCP와 UDP로 나뉜다.

<img src="https://velog.velcdn.com/images/tkdcjf38/post/570037da-1d29-4ba2-9bf7-211d242141f4/image.jpeg">

## Udp 특성

> UDP는 client와 server 간에 datagram의 신뢰할 수 없는 전송을 제공한다.  
*신뢰할 수 없는 전송이란 분실이 일어날 수 있음을 의미한다

- UDP는 application 관점에서 처리하는 단위가 datagram인 서비스이다.
- UDP통신을 위해서는 datagram을 만들어야하고 datagram을 목적지로 내보내는 socket을 UDP Socket이라고 한다.
- UDP는 data를 보내기 전에 connection을 설정할 필요가 없기 때문에 handshacking을 하지 않아도 된다.
- 송신자는 목적지의 IP주소와 port number를 명시적으로 첨부해야 하며, 수신자는 수신된 패킷에서 송신자의 IP 주소 및 port number를 추출할 수 있다.
- UDP는 매우 단순해서 서비스의 신뢰도가 낮다.
- datagram이 인터넷 상에서 분실 될 수도 있고 여러 datagram을 보냈다면 순서가 뒤바뀔 수 도 있다.   

## Tcp 와 Udp의 차이점?

| Tcp | Udp |
| --- | --- |
| 연결형 프로토콜 | 비연결형 프로토콜 |
| 데이터의 경계를 구분하지 않음	| 데이터의 경계를 구분함 |
| 신뢰성있는 데이터 전송 (데이터 재전송 존재O) | 비신뢰성 데이터 전송 (데이터 재전송 존재X) |
| 일 대 일(Unicast) 통신 | 일 대 일, 일 대 다(Broadcast), 다 대 다(Multicast) 통신 |
### 그래서?
TCP는 연속성보다 신뢰성 있는 전송이 중요할 때에 사용되는 프로토콜이며,
UDP는 TCP보다 빠르고 네트워크 부하가 적다는 장점이 있지만 신뢰성 있는 데이터 전송을 보장하지는 않다.
그렇기 때문에 신뢰성보다는 연속성이 중요한 실시간 스트리밍과 같은 서비스에 자주 사용된다.

## Android에서의 구현
### permission
```xml
<uses-permission android:name="android.permission.INTERNET"/>
```
Socket도 Internet 연결을 하기 때문에 Internet permission이 ~~당연히~~ 필요하다

### socket Receive

```kotlin
thread{
    val socket = DatagramSocket(SERVER_PORT)  //SERVER_PORT is Int
    val receiveMsg = ByteArray(256)
    val serverPacket = DatagramPacket(receiveMsg, receiveMsg.size)
    while(true) {
        socket.receive(serverPacket)          //receive가 되기 전까지 여기서 대기!
        val receiveData = String(serverPacket.data, 0, serverPacket.length)
        doSomething(receiveData)
    }
}
```
### socket Send

```kotlin
thread{
    val sendPacket = DatagramPacket(
        sendMessage.toByteArray(),
        sendMessage.length,
        address,
        port
    )
    socket.send(sendPacket)
}
```