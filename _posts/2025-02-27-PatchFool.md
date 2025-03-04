---
title: "[Review] PatchFool"
category: [paper]
tag: [attack]
comments: true
toc: true
toc_label: 목차
toc_sticky: true
---
# Introduction
ViT가 vision task에서 좋은 성능을 보여주고 있고 real world에 적용하기 위해 robustness가 중요하다.
선행 연구에 따르면 attention이 이미지의 전역적 연관성을 고려하기 때문에 CNN보다 robust하다.
하지만 ViT 구조에 맞는 perturbation을 주입한다면 어떻게 될지 질문을 던진다.
이를 검증하기 위해 self-attention 메커니즘을 이용해 perturbation을 주입하는 PatchFool 공격을 제안한다.

# 제안 방법
## Attack Objective Formulation
Perturbation의 강도에 제한을 두지는 않고, 공격할 패치의 숫자만 제한을 둔다.
Loss 함수 $J$와 입력 $X=[x_1,...,x_n]\in R^{n\times d}$에 대해 공격 objective는 다음과 같다.   
$$
arg max_{p,E}\ J(X+1_p\cdot E, y)
$$   
여기서 $E$는 perturbation이고 $1_p$는 $E$를 적용할 위치를 나타낸 mask이다.
위 수식의 파라미터는 $p$와 $E$이기 때문에 공격이 성공할 perturbation과, 그것을 적용할 위치를 찾는 것이 관건이다.

## Attention-Aware Patch Selection
패치를 적용할 위치 $p$를 선정하기 위해 attention을 사용한다.
이 논문에서 사용한 방법은 attention map에서 top-K 패치를 선택하는 것이다.
우선 $n\times n$ 크기의 attention map이 있을 때, 이 행렬 M은 다음과 같이 $n$개의 열벡터로 표현할 수 있다.

$$
M=[c_1, ..., c_n]\\
(importance\ of\ i^{th}\ patch)=\Sigma_j\ c_{ij}$$

열벡터 $c_i$는 i번째 토큰에 대한 다른 모든 토큰과의 연관도를 의미하기 때문에, 평균을 내면 i번째 토큰의 중요도를 나타낼 수 있다. 
여기서 $c_{ij}$는 스칼라로 열벡터 $c_i = [c_{i1},...,c_{in}]$의 원소이며, i번째 패치의 중요도 중 상위 K개의 인덱스를 선택하여 패치를 적용할 위치를 결정한다.

다만 multi-head attention을 사용하고, 여러 layer가 있기 때문에 고려사항이 더 생긴다.
여러 head에 대해서는 모두 평균을 내어 사용하고, 여러 layer에 대해서는 특정 layer을 선택한다.
논문에서는 layer가 깊어질수록 다른 패치와의 정보 교환이 많아 입력과 차이가 커져 입력 선택에 이상적이지 못하기 때문에, 다섯번째 layer를 고정해서 사용한다.

코드로 보면 다음과 같다.
```python
atten_layer = atten[atten_select].mean(dim=1) # attention map에서 특정 layer 선택 후, head에 대해 평균 [B,H,L,N,N] -> [B,N,N]
atten_layer = atten_layer.mean(dim=-2)[:, 1:]   # 열벡터의 평균을 내고 cls 토큰 빼고 선택 [B,N,N] -> [B,N]
p = atten_layer.argsort(descending=True)[:, :args.num_patch] # 내림차순으로 정렬 후 상위 K개 패치의 인덱스 선택
```

## Attention-Aware Optimization
위에서 패치를 적용할 위치 $p$를 결정했고, 이번에는 어떤 $E$를 넣을지 결정해야 한다.
이 논문에서 선택한 방법은 다음 수식과 같이 중요한 패치 $p$의 중요도를 최대화 하는 것이다.
$$
\begin{aligned}
J_{Attn}(X,p) &= \Sigma_j c_{ij}\\
J(X+E, y,p) &= J_{CE}(X+E,y)+\alpha J_{Attn}(X+E,p)\\
argmax_{p,E}\ &J(X+E,y,p)
\end{aligned}
$$

여기서 $\alpha$는 $J_{Attn}$의 중요도를 조절하는 하이퍼파라미터이고 0.002로 설정한다.
$E$를 업데이트 하는 방법은 PGD의 방식을 따른다.

* 중요한 패치의 중요도를 낮추는 방법으로 optimization을 한다면?

## Sparse PatchFool & Mild PatchFool
Sparse PatchFool은 하나의 패치 안에 조작할 픽셀 개수에 제한을 두는 방식이고, Mild PatchFool은 perturbation의 epsilon에 제한을 두는 방식이다.
