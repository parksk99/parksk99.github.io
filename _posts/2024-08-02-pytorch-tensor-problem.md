---
title: "pytorch 텐서의 디바이스 이동 시 값 초기화 문제"
category: [AI]
tag: [pytorch]
comments: true
toc: true
toc_label: 목차
toc_sticky: true
---

# 문제 상황
pytorch에서 텐서를 생성한 후 다른 디바이스로 옮길 때 값이 0으로 초기화되는 문제가 발생   
![problem](/assets/pytorch_tensor_problem/problem.png)   

# 환경
* GPU: RTX A5000
* pytorch 버전: 2.4.0+cu121   
* cuda 버전: 11.0   
* 그래픽 드라이버 버전: 535.183.01   

# 해결 방법
pytorch, cuda, 드라이버의 버전 호환성 문제로 추정하고 아래와 같이 버전 변경   
* pytorch: 2.1.2+cu121
* cuda: 11.8
* 그래픽 드라이버: 525.147.05

## pytorch 버전 변경 방법
### 삭제 후 설치
```sh
pip uninstall torch # 기존 버전 삭제
pip install torch==2.1.2+cu121 torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu121 # 새로 설치
```
### 설치 확인
```python
import torch
print(torch.__version__)
```

## 드라이버 버전 변경 방법
### 기존 드라이버 제거
```sh
sudo apt-get purge nvidia*
```
### NVIDIA 드라이버 PPA 추가
```sh
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
```
### 설치 가능한 드라이버 버전 확인
```sh
ubuntu-drivers devices
```
![ubuntu_drivers](/assets/pytorch_tensor_problem/ubuntu_drivers.png)   
위 그림에서 원하는 버전 선택
### 드라이버 설치
```sh
sudo apt-get install nvidia-driver-525
```

### 설치 확인
```sh
sudo shutdown -r now # 재부팅
nvidia-smi
```