---
title: 네트워크 계층
category: [CS]
tag: [network]
toc: true
toc_label: 목차
toc_sticky: true
---
네트워크 계층은 소프트웨어에서 생성된 데이터를 네트워크를 통해 통신하기까지 필요한 과정을 단계별로 구분한 것이다. 전송시에 상위 계층에서부터 '헤더'라는 살을 붙여가며 하위 계층으로 내려가면, 최종적으로 어떤 정보가 어디로 가야하는지에 대한 내용이 담긴 전기 신호로 바뀌게 된다. 
반대로 데이터를 수신할 때는 하위 계층에서 올라오면서 어떤 정보가 담겼는지 풀어낸다.

# 네트워크를 구분하는 관점
네트워크의 계층을 구분하는 두가지 관점이 있다.
하나는 OSI(Open System Interconnection) 7 Layer 이고, 하나는 TCP/IP 4 Layer이다. OSI 7L ayer는 개념적 모델로 De jure에 가깝고 TCP/IP 4Layer는 De facto라고 할 수 있다.   

OSI 7 Layer에서 몇가지 항목이 병합된 것이 TCP/IP 4 Layer이기 때문에 7 Layer부터 살펴본 후 4 Layer를 간략히 정리해 보겠다.

# OSI 7 Layer
![osi7layer](/assets/network_layer/osi7layer.png)   

<br>
전체적인 구조는 위 그림과 같다. 아래 계층부터 천천히 살펴 보겠다.
## Physical Layer(Layer 1)
상위 계층에서 전달받은 디지털 정보를 아날로그인 전기적 신호로 변환하는 기능을 담당한다.
반대로 수신할 때는 전기적 신호를 디지털 신호로 변환하는 역할을 한다.   
1계층을 구성하는 하드웨어는 리피터, 허브, 라우터 등이 있다.

## DataLink Layer(Layer 2)
신뢰성 있는 데이터를 송수신하기 위한 계층이다. 데이터가 손실되는 등의 오류가 생기면 데이터 링크 계층에서 감지해낸다.
물론 상위 계층에서도 데이터의 완전성을 검증할 수 있지만 그렇게 되면 오류가 발생한 경우에 불필요한 오버헤드가 발생하게 된다.
밑단에서 오류를 감지하고 굳이 위로 올려보내지 않게 만드는 것이다.


2계층에서는 이더넷을 사용한다. 이더넷은 CSMA/CD(Carrier Sense Mutiple Access with Collision Detection) 방식을 사용한다.
CSMA/CD란 간단히 설명하면 여러 호스트가 하나의 허브에 연결되어 있을 때 동시 접근을 제어하는 방식이다.
동시에 데이터를 전송하려고 해서 충돌이 나면 허브가 충돌이 발생했다는 Jam Signal을 보낸다. 그럼 호스트들은 각자 랜덤한 시간을 기다렸다가 전송을 시도하는 방식이다.

## Network Layer(Layer 3)
LAN(Local Area Network)를 벗어난 통신을 할 때 사용된다. IP주소를 가지고 ARP(Address Resolution Protocol)를 통해 상대방의 
MAC주소를 알아낸다.   

라우터가 3계층에서 작동하는데 라우팅에는 두가지 방법이 있다. 정적 라우팅과 동적 라우팅.
정적 라우팅은 다음에 어떤 라우터를 선택할지 관리자가 미리 설정하는 것이다. 따라서 길 선택 알고리즘이 없기 때문에 부하가 적은 장점이 있지만 네트워크 상황이 바뀌면 설정을 다시 해야하는 단점이 있다.   
반면 동적 라우팅은 네트워크의 상태를 스스로 판단해서 다음 라우터를 선택하는 것이다. 새로운 라우터가 추가되는 등의 유동적인 상황에 잘 적응할 수 있지만 주기적으로 상태 정보를 주고 받아야 하기 떄문에 대역폭을 낭비해게 되는 단점이 있다.


IP헤더에 대한 것은 따로 빼서 정리해야겠다.

## Transport Layer(Layer 4)
정해진 길이가 아닌 가변길이 데이터를 전송할 수 있는 계층이다. 대표적으로 연결지향인 TCP와 비연결지향인 UDP 프로토콜이 존재한다.   
TCP와 UDP도 따로 빼서 정리.

## Session, Presentation, Applicatoin Layer(Layer 5, 6, 7)
위 세개의 계층은 TCP/IP 4 Layer에서 하나로 통합되어 구분된다. 
1. Session Layer   
연결을 구성하고, 관리하고, 종료하는 역할을 한다. 프로세스간의 통신을 관리한다고 할 수 있다.
2. Presentaion Layer   
데이터의 표현 차이를 극복하기 위한 계층이다. 예를 들어 JPEG, GIF 등 여러 데이터 포맷을 제대로 인코딩하고 디코딩하기 위한 것이다.
3. Application Layer   
사용자와 가장 가까이 존재하는 계층으로 사용자의 인터페이스를 구성하고 HTTP, FTP등의 프로토콜이 사용된다.



# TCP/IP 4 Layer
![TCPIP4Layer](/assets/network_layer/tcpip4layer.png)   
OSI 7 Layer에서 Session, Presentation, Applicatoin 세 계층을 하나로 통합하고
Datalink, Physical 계층을 하나로 통합한 것이 TCP/IP 4 Layer이다.
계층이 구분하기 모호하고 비슷한 역할을 하는 것이 있기 때문에 실제적으로 사용되는 모델이다.


TCP/IP 4 Layer에서 Network Interface 계층만 다시 두 계층으로 나누어서 5계층으로 구분하는 경우도 있다.
두 계층은 구분이 조금 더 명확할 필요가 있기 때문이라고 한다.