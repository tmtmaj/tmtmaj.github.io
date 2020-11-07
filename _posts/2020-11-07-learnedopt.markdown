---
title: "[NLP] Tasks, stability, architecture, and compute: Training more effective learned optimizers, and using them to train themselves 정리"
layout: post
author: Jeonghyeok Park
categories: Natural-Language-Processing
tags: Natural-Language-Processing
---

﻿

안녕하세요.

이 포스팅은 최근에 Google research에서 나온 논문에 대한 정리 내용입니다.

Tasks, stability, architecture, and compute: Training more effective learned optimizers, and using them to train themselves

Google Research, Brain Team

중국어 포스트를 중심으로 해서 원 논문의 내용을 추가했습니다.

---

## Abstart

현재는 다양한 tasks을 해결하는 방식이 **데이터와 모델에 대한 전문적인 지식을 기반으로 모델의 Feature를 손수 설정하던 방식**에서 **학습할 수 있는 신경망 모델 (Neural network model)**로 대체되었다. 우리는 이와 같이 모델을 train하는 방식을 learned algorithm이 바꿀 수 있다고 믿는다. 이 논문에서 우리는 사용자가 hyperparameter를 정할 필요 없이 다양한 task에 대한 모델을 훈련할 수 있는 general-purpose learned optimizers를 제안한다. 우리는 neural network으로 구성되어 있고, 계층적 구조를 가진 optimizer를 소개한다. 이 optimizer는 validation loss 같은 추가적인 feature에도 접근하여 알아서 모델 regulaization을 한다. 지금까지 제안된 대부분의 learned optimizer들은 single task나 small number of tasks에만 적용되었다. 우리가 제안한 learned optimizer는 수천개의 task에 대해 훈련되어 훈련해보지 않은 task에 대해서도 적용이 가능하다. 우리의 learned optimzier는 잘 작동하며, 기존의 first order optimizer과는 구별되는 행동을 학습한다. 예를 들어, learned optimzier는 implicit regularization이 된 update step을 생성하고 hyperparemter(e.g. batch size)나 architecture(e.g. neural network width)를 변경하는 문제에도 적용할 수 있다.


---


영어가 짧아서 그런지 abstract 첫 문장부터 이해가 되지 않았습니다. 전체적으로 보고 중국어 포스트도 읽고 나니 이해가 되었습니다. 그래도 막상 한국어로 옮겨적고 나니 한번에 이해하기는 어려울 것 같다는 생각이 들어 예를 들어서 말씀드리려 합니다.

번역 모델을 예로 들자면 이전에는 언어학자들이 각 언어의 문법적 구조, 사전, alignment 등 rules(features)을 정의하고 이를 기반으로 번역을 했습니다. "나는 너를 사랑해"를 영어로 번역하라고하면, rule-based mahine translation system은 먼저 사전을 기반으로 대응되는 단어 "I you love"를 추출하고, 한국어는 SOV 구조이지만 영어는 SVO 구조라는 규칙을 활용하여 최종적으로 "I love you"로 번역할 수 있습니다. 반면, Neural network을 사용하는 RNN-based MT model, Transformer는 모델을 구성하고 한국어 문장, 영어 문장을 넣어주면 스스로 학습합니다. 간단하면서(각 언어에 대한 지식 불필요) 비용도 상대적으로 매우 적은(병렬 코퍼스) 이 Neural network-based MT system은 과거의 rule-based MT system보다 훨씬 좋은 성능을 이끌어냈습니다.

이 논문은 이보다 한 발짝 더 나아갑니다. 모델의 구성마저도 알아서 학습하도록 하는 general-purpose learned optimizers를 제안합니다. 즉, 기존에 프로그래머가 설정하던 hyperparameters (e.g. batch size) 나 architecture (e.g. neural network width)마저도 알아서 학습하는 것입니다.

learned optimizer를 제안한 논문이 있었으나, 소수의 정해진 task에 대해서만 적용이 가능했습니다. 하지만 이 논문에서 제안한 learned optimizer는 6000개의 다른 task/다른 model에 대해서 optimizer를 훈련시켰고, 그 결과 이전의 learned optimizer보다 나은 generalization를 달성했다고 말합니다. 예를 들어, Model1은 문장 분류를 위한 RNN 모델을 사용하고 데이터 셋은 YELP-5이고, Model2는 이미지 분류를 위해 CNN 모델을 사용하고 데이터 셋은 CIFAR-10... 6000개의 Model의 각기 다른 task/neural network을 사용하여 optimizer를 훈련합니다.

또, learned optimizer의 새로운 구조를 제안합니다. optimizer를 학습하는 과정에서 더 많은 정보 (e.g. validation loss 등)를 효과적으로 처리할 수 있도록 계층적 구조를 제안합니다. (LSTM + FF 의 구조)

---

## The three barriers to learned optimizers

기존의 learned optimizer는 다음 3가지 문제점이 있다고 합니다.

1. Computational scale: optimizer를 훈련시키는 것은 비용이 상당하다.

2. Train tasks: model를 훈련시키는 것처럼 optimizer를 훈련할 때도 많은 데이터가 필요하다.

3. Inductive bias of optimizer architecture: 기존의 learned optimizer가 단순하여 task information을 제대로 활용하지 못했다. 계층적 구조의 learned optimizer를 활용하여 이 문제를 해결한다.

---

## Methods: Addressing the three barriers to learned optimizers

optimizer의 training은 bilevel optimization problem입니다.

즉, 두 가지 최적화 문제 loop를 가지고 있습니다.

첫번째 loop는 한 task에 대한 모델을 훈련시키는 것에 대한 것 (inner loop)

두번째는 learned optimizer를 반복적으로 update하기 위한 것 (outer loop)

inner-loss : 한 task의 loss function과 관련

outer-loss : inner-task로부터 얻은 정보를 사용하여 learned optimizer를 훈련하는 loss function과 관련

inner-parameter/outer-parameter/inner-optimizer/outer-optimzier

주의하실 점은 아래 이 논문에서 제안한 방법은 inner-/ outer-에 대한 개념이 확실히 서야 제대로 이해하실 수 있습니다.


위에 말씀드린 3가지 문제점을 해결하기 위해서 이 논문에서는 세가지 방법을 제안합니다.

#### 1. Outer-Optimization

일반적인 신경망 모델처럼 outer-optimizer는 outer loss를 최소화하는 방향으로 학습됩니다. 하지만 신경망 모델의 training처럼 모델의 weight 조정을 위해 back-propagation을 할 수는 없습니다. 6000개의 모델에서 얻은 loss를 미분하여 gradient를 구할 수는 있지만 그 비용이 너무 크기 때문입니다. 그리고 outer-loss의 surface가 매우 거칠기(?) 때문입니다. (제 생각에 이 문장은 모델들의 inner-loss 크기 격차가 너무 크기 때문이 아닐까 싶습니다. 원문: In addition, the outer-loss surface is badly conditioned and extremely non-smooth, making it difficult to optimize.)

따라서 논문에서는 derivative-free optimization(evolutionary strategies, Persistent Evolotionary Strategies)을 사용하여 outer-optimizer의 optimization 비용을 낮춥니다. 이전에 제안된 learned optimizer는 약 20 unrolled step으로 제한했던 반면, 이 논문에서 제안된 learned optimizer는 gradient를 계산하지 않고 outer-loss를 최적화할 수 있기 때문에 240~360 steps으로 그 optimizer를 트레이닝할 수 있습니다.

#### 2. Task distributions

어떤 inner-task에 대해 outer-optimizer를 훈련하는 지는 generalization과 computational complexity에 대해 중요합니다. 훈련 동안 보지 못한 새로운 task(Out-of-task)에 대해 learned optimizer를 잘 적용될 수 있도록 하기 위해 저자는 대표성을 띠는 task들을 outer-optimizer 훈련에 사용했습니다.

훈련에서 사용된 inner-model은 다음과 같습니다.

RNNs, CNNs, masked auto regressive flows, fully connected networks, language modeling, variational autoencoders, simple 2D test functions, quadratic bowls, and more

각 task에 대해 4가지 dataset

- training data : inner model 훈련을 위한 데이터셋

- inner-validation data: learned optimizer으로 inner-model의 validation loss를 계산하기 위한 데이터 셋

- outer-validation data: learned optimizer의 weight를 update하기 위한 데이터셋

- test data: 훈련된 learned optimizer를 테스트하기 위한 데이터 셋

outer-loss normalization: 각 task에 대한 각 모델의 loss 사이의 크기는 매우 다르기 때문에 normalization

#### 3. Optimizer architecture

저자는 learned optimizer의 구조도 중요하다고 말합니다.

![]({{ site.baseurl }}/assets/learnedopt1.png )

위 그림에서 (c)가 이 논문에서 제안한 learned optimizer의 구조입니다.

per-tensor LSTM + per-parameter FF로 구성되어 있습니다.

기존 모델 대비 더 많은 정보 ( train/ valid loss 등)을 활용할 수 있다는 것을 확인할 수 있습니다.

![]({{ site.baseurl }}/assets/learnedopt2.png )

중국어 포스트를 참고하여 그린 전체적인 훈련 구조입니다.

1. training dataset으로 inner model들을 훈련

2. inner validation dataset에 대한 inner model의 loss function을 계산

3. inner model의 loss function의 평균을 구함

4. outer-validation dataset으로 derivative-free optimization을 진행하여 learned optimizer를 optimize

---


## Experimental Results

![]({{ site.baseurl }}/assets/learnedopt3.png )

global은 모든 모델의 hyperparameter가 고정된 것을 의미하고 per Task는 모델마다 다른 hyper parameter를 사용했다는 것을 의미합니다. 그림에서 단색 막대는 training set에 대한 결과이고 빗금 쳐진 부분이 test set에 대한 결과입니다.

이 외에도 여러 실험을 진행했지만 그 중에 가장 흥미로운 실험 하나를 꼽아서 말씀드리려합니다.

저자는 learned optimizer(trained)로 새로운 learned optimizer(new)를 훈련했을 때, 그 learned optimizer(new)가 learned optimizer(trained)와 같아지면 이 learned optimizer(trained)는 훈련이 잘되었다고 주장합니다. 말이 되고 모델로부터 학습된 learned optimizer로 다시 learned optimizer를 다시 훈련시킨다는 것이 매우 흥미롭습니다.

![]({{ site.baseurl }}/assets/learnedopt4.png )

실험 결과는 희망적이지는 않습니다. 100000 iteration까지는 Adam과 비슷하게 훈련이 되지만, 그 이후로는 오히려 성능이 감소하는 것을 확인할 수 있습니다. 저자는 이것이 inner-iteration 이 더 필요하다는 반증이라고 주장합니다.

![]({{ site.baseurl }}/assets/learnedopt5.png )


---


## 고찰


inner/outer에 대한 정의가 잘 서지 않아 여러번 정독했던 것 같습니다. 모니터만 보면서 한 문단, 두 문단.. 졸고 있었네요. 용어 개념도 명확히 안 선채 머리 속으로만 이해하려 했던게 실수였던 것 같습니다. 메모하면서 읽어보니 그때서야 좀 이해가 되더군요.

역시 갓구글에서 나온 논문이라 그런지 실험도 많고 입이 떡 벌어질 정도의 폭넓은 모델 활용에 또 갓구글을 외쳤습니다.

모델 훈련 아니 이제는 뭐라그래야할까요.

Feature를 정의하여 모델링 > hyper-parameter를 정하여 Model을 구성하고 데이터만으로 훈련 > learned optimizer로 hyperparameter로 알아서 정의 > ??

사실 learned optimizer 자체에도 프로그래머가 정의해줘아하는 부분이 있고, 6000개의 모델 훈련을 적절히 빠르게 위해 update step, outer-optimizing method 등 제약하는 부분들도 있지만 확실히 더 한단계 나아간 것 같은 생각이 듭니다.

사실 요즘은 deep-learning을 위한 open source들이 잘 나와 있어서 코드 몇 줄이면 모델을 훈련시키는 데 전혀 문제가 없습니다. 하지만 hyper-parameter는 전적으로 프로그래머가 정의해줘야하는 부분이죠. 실제로 성능에 크게 영향을 미치는 부분이고요. 어떤 task, 어떤 model에 잘 적용되고 대충 이렇게 해도 항상 중상의 성능을 보여주는 것도 있지만, Adam optimizier? 더 좋은 성능을 위해서는 hyperparameter를 조정하는 것이 필수입니다.

하지만 살짝 두렵기도 하네요. 기계 학습을 공부하고 있는 데 설 자리가 좁아지는 느낌도 듭니다. 앞으론 더 열심히 해야겠습니다.

---


출처:

https://mp.weixin.qq.com/s/f2AGfX4uyST8XqB5yNesYw


https://arxiv.org/pdf/2009.11243.pdf


﻿