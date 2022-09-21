---
title: vmware16 포트 포워딩 방법
category: [linux]
tag: [linux]
toc: true
toc_label: 목차
toc_sticky: true
---
vmware에서 포트포워딩을 하고 싶어서 검색을 해보니까, 버전이 안맞는지 설정하는 메뉴가 안보였다.
근데 막상 하고 보니 쉬워서 간단히 정리한다.   

사용 환경은 비상업버전인 VMware Workstation Player 16 버전이고 가상환경 OS는 Ubuntu 20-04 버전이다.

# 방법
NAT 설정 파일은 보통 C:/ProgramData/VMware/vmnnetnat.conf 여기 있다. 이 파일을 열어서 다른건 신경쓰지 않고 [incommingtcp] 항목 아래에 ```(열어줄 포트 번호) = (목적 가상환경 ip:port)``` 형식으로 적어준다.   
![example](/assets/vmware/example.jpg)   
위 사진처럼 사용하는 가상환경이 여러개라면 그 ip 주소에 맞게 다른 포트를 열어주면 된다.