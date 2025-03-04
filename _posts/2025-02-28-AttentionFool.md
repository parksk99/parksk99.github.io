---
title: "[Review] AttentionFool"
category: [paper]
tag: [attack]
comments: true
toc: true
toc_label: 목차
toc_sticky: true
---
# Introduction
Attention이 트랜스포머가 언어 처리와 이미지 처리의 다양한 분야에 좋은 성능을 거두는데 큰 역할을 했다.
최근 비전 트랜스포머는 분류, segmentation, object detection, 비디오 이해 등 다양한 task를 수행하기 위해 각기 다른 구조를 택하지만,
dot-product attention은 공통적인 핵심 역할을 한다.   
비전 트랜스포머의 강건성을 평가하기 위한 다양한 연구가 있었고, CNN 보다 더 강건하다고 말한다.
하지만 다른 연구에서는 비전 트랜스포머도 적대적 공격에 취약하다고 언급하고, CNN에 특화된 공격 방법이 비전 트랜스포머에 최적이 아니라고 말한다.
최근 다양한 연구가 비전 트랜스포머의 강건성을 평가하기 위한 공격 방법을 탐구하고 있고, dot-product attention이 공격에 취약하다고 알려져 있다.

Dot-product attention은 쿼리와 키 토큰간의 연관성을 dot-product로 계산하고 softmax를 거쳐 정규화한 뒤 밸류 토큰으로 곱하는 방식을 따른다.
일반적으로 dot-product attention의 적대적 기울기를 계산해 공격하는데, 이것은 softmax를 통해 정규화되어 값이 작아지기 때문에 원래 기울기보다 더 작은 값을 갖는다.
그래서 매우 적거나 없는 적대적 효과를 유발해 공격이 효과적으로 일어나지 않는 경우가 발생한다.

이 문제를 해결하기 위해, 이 논문은 효과적인 loss를 제안하고, 공격의 성공에 영향을 미치는 요인을 분석해 비전 트랜스포머를 속이는 AttentionFool을 제안한다.

# Preliminaries
* 공격 objective formulation
$$arg\ max_p\ J(f(x,p,L), y)$$
J는 loss function, p는 adversarial patch, L은 location를 의미한다. 적대적 패치를 위치시킨 이미지를 모델에 넣어 구한 loss 값을 최대화하며 p를 업데이트하는 방법이다.

* 패치 최적화 알고리즘: $PGD_\infty$로 패치 최적화

* Dot-product attention
$$
Attention_h(Q,K,V)=softmax(\frac{QW_Q^h (KW^h_{k})^{T}}{\sqrt{d_k}})VW_V^h
$$
헤드에 대한 attention 연산은 위 수식과 같고, softmax를 통해 얻은 attention weight를 아래와 같이 $A_h$로 표현한다.
또한 Q,K,V가 모두 동일한 self attention은 아래와 같이 표현할 수 있다.

$$
\begin{aligned}
A_h(X) &= softmax(\frac{QW_Q^h (KW^h_{k})^{T}}{\sqrt{d_k}})\\
SelfAH_h(X) &= A_h(X)XW_V^h
\end{aligned}
$$

# Dot-Product Attention의 강건성
PGD같은 공격이 dot-product self-attention에 강력하지 못한 이유를 설명.

## Dot-Product Attention의 기울기
공격 objective에 따라 패치p를 업데이트 하려면 전체 모델에 대한 기울기가 필요하다. 각 레이어에 대한 self attention에 대한 기울기가 필요하다는 것이고, 그것은 다음과 같이 쓸 수 있다.
$$
\begin{aligned}
\nabla_x SelfAH_h(X) &= \nabla_x A_h(X)XW_V^h + A_h(X)W_V^h\\
&= [(\nabla_x A_h(X))+A_h(X)1_X]W_V^h
\end{aligned}
$$

그래서 self-attention에 대한 기울기는 $|\frac{\nabla_x A_h(X)}{A_h(X)1_X}|$에 영향을 받고, 이 값의 각 레이어에 대한 중간값을 계산했다.
그 결과 대부분 $\nabla_x A_h(X)$가 $A_h(X)1_X$의 20분의 1 정도의 값을 가지는 일관성을 보였다.
그 말은 $\nabla_x SelfAH_h(X) \approx (A_h(X)1_X)W_V^h$라고 말한다. 
따라서 적대적 공격은 $W_V^h$로부터 선형적으로 전파되는 적대적 특징에 편향되며, $A_h(X)$로부터 비선형적으로 전파되는 적대적 특징을 무시하는 경향이 생길 수 있다.

다시 말해서, self attention의 기울기는 $A_h$와 $W_V^h$ 두 개의 변수에 의해 결정된다고 근사할 수 있는데, 선형적인 $W_V^h$로부터 생기는 적대적 특징에 집중하고, 
softmax를 거치기 때문에 비선형적인 $A_h$로부터 전파되는 적대적 특징을 무시하는 경향이 생길 수 있다는 것이다.

## Dot-Product Attention Weight의 강건성
적대적 공격에 의해 attention weight인 $A_h(X)$가 어느 정도 영향을 받을지 분석한다. 통제된 상황을 만들기 위해 $X$는 평균이 $\mu$이고 분산이 1인 가우시안 노이즈를 사용했고, $W_Q=-w\cdot I_{d_k}, W_K=w\cdot I_{d_k}$를 사용했다.
$W_Q$와 $W_K$는 부호만 다른 대각행렬이다.
여기서 공격자는 오직 $L_\infty$ 제약을 하에서 $X_0$만 수정할 수 있다.
공격자의 목표는 $A_h(X^{adv})_{0j}$가 0.99를 넘는 값이 95퍼센트를 넘도록 하는 것이다. 다시 말해서, 첫 번째 키와 attend하는 쿼리의 95퍼센트가 0.99를 넘도록 하는 것이다.

![Image](https://github.com/user-attachments/assets/8f631b99-b839-42d5-b699-dca6a04f9de5)

위 공격 목표를 달성하기 위한 $\epsilon$ 값은 위 그림처럼 $\mu, w, d_k$에 따라 달라진다.
우선 각 그래프에 따르면, $d_k$가 커질수록, $w$가 클수록 작은 $\epsilon$값이 필요하다. 그리고 세 그래프를 비교하면 $\mu$가 작을수록 적은 $\epsilon$이 필요하다는 것을 알 수 있다.
정리하면 다음과 같다.
* 모델의 크기가 커질수록 공격하기 쉽다.
* $A_h$의 활성화 정도가 클수록 공격하기 쉽다.
* 입력 $X$의 값이 0으로 몰리지 않을수록 공격하기 쉽다.

세 번째 finding은 $\mu$가 커질수록 키-쿼리 쌍 사이의 거리를 멀어지게 한다는 것을 의미한다. 그 이유는 다음과 같다.
쿼리와 키의 평균을 각각 $\mu_Q, \mu_K$라고 하면, $\mu_Q=mean(W_Q\cdot X)=-w\mu$이고 $\mu_K=w\mu$이다.
각 쿼리와 키 사이의 원소별 평균 거리 $|\mu_K^i-\mu_Q^i|$는 $|w\mu_i + w\mu_i| = 2|w||\mu|$이다.
$\mu$가 커질수록 원소별 평균 거리도 커져 키-쿼리 쌍의 거리가 멀어진다는 것을 말한다.
따라서 하나의 키가 모든 쿼리와의 연관도가 높아질 수 있다.

대부분 트랜스포머 모델은 큰 $d_k$를 사용하고, layer norm을 통해 평균을 0이 아닌 $\mu$로 정규화하며, 많은 비전 트랜스포머의 $A$가 매우 큰 하나의 값을 가지는 것을 관찰함.
그래서 dot-product attention이 패치 공격의 취약점으로 작용할 것이라고 예상함.

# AttentionFool
$L_{kq}$: 모든 레이어의 헤드를 공격   

기본 메커니즘은 특정 키에 연관되는 모든 쿼리의 연관도를 최대화하는 방식.
특정 레이어 l의 특정 헤드 h가 있을 때, 그것의 loss 함수 $L^{hl} = \frac{1}{n} \sum_j B^{hl}_{ji^{*}}$이며, $L$을 최대화하는 방향으로 최적화함으로써 공격한다.
이것을 모든 레이어와 모든 헤드로 확장하려면 $L^{hl}$을 평균내어 $L$을 loss값으로 사용할 수 있는데, 이것은 최적의 값이 아닐 수 있기 때문에 최댓값을 선택하는 방식을 따른다.
즉, $L^l_{kq}=max_h(L^{hl}_{kq})$이고 $L_{kq}=max_l(L^l_{kq})$이다. 다만 여기서 $max_x$함수는 단순 최댓값 선택 함수가 아니라 smoothing된 함수로 $max_x=log\Sigma_ie^{x_i}$를 사용한다.
x를 지수함수에 넣어 클수록 더 많은 영향을 주도록 하고, 그것을 모두 더한 뒤 log를 씌워 값을 안정화하는 smoothed max 함수이다.
그리고 각 헤드와 레이어가 loss 함수에 비슷하게 기여하도록 만들기 위해 Q,K를 헤드와 레이어 별로 정규화한다.

$L_{kq^*}$: cls 토큰 공격

To Do

# Evaluation
ViT-T 제외 기존 공격보다 더 좋은 공격 성공률을 보임
# Discussion
* ViT에 PGD 공격하면 정확도가 0%대로 떨어지는데 뭐가 ViT가 CNN보다 강건하다고 하는지?
* 왜 단순 gradient 기반 공격이 비전 트랜스포머에 강력하지 못한지, 왜 dot-product attention이 취약점으로 작동할 것인지에 대해 이론적으로 검증함.