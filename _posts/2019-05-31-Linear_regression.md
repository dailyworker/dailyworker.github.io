---
title: 선형 회귀(Linear Regression)란 무엇인가?
date: 2019-05-31 16:30:00 +0900
tags: 
  - AI(Artificial Intelligence)
  - ML(Machine Learning)
  - Linear Regression
---

# Dive into Linear Regression
+ STEP 1. 선형분석(Regression Analysis)
    + STEP 1.1 선형 모델
+ STEP 2. 번외 : 편향(bias)과 분산(variance)
+ STEP 3. 비용함수(Cost Function)
    +  STEP 3.1 오차(Error)와 제곱근 오차(Sqaure Error)
    +  STEP 3.2 비용함수(Cost Function)와 평균 제곱근 오차(Mean Sqaure Error)의 관계
    +  STEP 3.3 최소 평균 제곱근 오차(MMSE, Minimum Mean Square Error)
+ STEP 4. 경사하강법(Gradient Descent)
+ STEP 5. 학습계수(Learning Rate) ɑ는 어떻게 설정해야될까?

# 개요

저희 [Nest.net](http://nnet.cbnu.ac.kr)에서는 지금 현재 인공지능 스터디를 진행하고 있습니다. 진행하면서, 인원들이 질문을 가장 많이했던 선형 회귀에 대해서 포스팅을 남기게 됐습니다. 다시 올려드리겠지만, 이 포스팅을 읽기 전에 선형 모델에 대한 내용을 구글링하시면 좀 더 이해가 빠르실 겁니다. 

오늘은 선형 회귀와 Tensorflow의 기본 변수형들을 집중적으로 파헤친 후에 선형회귀 코드를 리뷰하는 식으로 넘어가고자 합니다.

## STEP 1. 선형분석(Regression Analysis)

먼저 2주차에 **선형 모델** 에 대해서 배웠던 것을 생각하자. 그리고 선형 분석을 알아보려고 한다.

선형 분석은영국의 유전학자 Francis Galton이 유전의 법칙을 연구하다 나온 것에 기인하게 된다. 연구의 내용은 **부모와 자녀의 키 사이의 관계** 였는데 연구결과로, 아버지와 어머니의 키의 평균을 조사하여 표로 나타낸 결과 자녀의 키는 엄청 크거나 작은 것이 아닌 그 세대의 평균으로 돌아가려는 경향이 있다는 것을 발견하였다.

Galton은 이를 **회귀 분석(Regression Analysis)** 라고 명칭하였다.

그렇다면? 머신러닝에서는 어떻게 회귀 분석 모델(= 선형 모델)을 적용하는 것일까?

우리는 이전에 수 많은 예시를 들어서 설명했다. 어느정도 집 값에 대한 내용이 있을 때 이런 평수일때는 집 값은 어떠할지? 3개월 내 채무이행할지? 안할지에 대한 내용들이 전부 선형 모델에 속한다.

일단, **선형 모델은 데이터의 분포가 하나의 선안에 표현될 수 있을 때 최적의 모델을 찾는다.**

그리고 **우리가 학습한 데이터를 통계로 어떤 임의의 점이 평면 상에 그려졌을 때 최적의 선형 모델(선)을 찾는 것이 목표이다.**

![그림1](https://t1.daumcdn.net/cfile/tistory/2277FC3859149DF40A)

이는 우리가 중고등학교때부터 배웠던 일차 함수의 그래프와 일치한다고 볼 수 있다.

$$f(x)\quad =\quad Ax\quad +\quad b$$

당연하게도 A는 선의 기울기를 나타내며, b는 선의 모양 축의 시작이라고 볼 수가 있다.

![그림2](https://t1.daumcdn.net/cfile/tistory/25047347589EC0592F)

이를 머신러닝에서 적용하는 수식으로 살펴보면 아래와 같다.

$$H(x)\quad =\quad Wx\quad +\quad b$$

여기서 H를 우리는 앞으로 **가설(Hypothesis)** 을 의미한다 할 것이며, W는 **가중치(Weight)** 라고 부르며, b는 **편향(bias)** 라고 말할 것이다.

편향(bias)말고도, 분산(variance)이라는 용어도 자주 보게 될텐데 
일단 아래와 같이 생각하고 추후에 얘기를 다시 하도록 하자.

## STEP2. 번외 : 편향(bias)과 분산(variance)

> 예측값들과 정답이 대체로 멀리 떨어져 있으며, 결과의 편향(bias)가 높다고 말하고,
예측값들이 자기들끼리 대체로 멀리 흩어져있으면, 결과의 분산(variance)가 높다고 말한다.

이래도 잘 모르겠으면 아래 그림을 보자.

![그림3](https://lh3.googleusercontent.com/rq_iMVSuIK1K4ykF9RQnF05hH6xxWm3lmNPWuQ3hfK9r4-3GBIuCxCW3L7QH53M3EIwbVWOcaRiRLDc0AIJ-0uq8-qzavpSWPceQ1lchq-ZPF16l3KLst24-x6MbGYFqQbEJmEI3gEc)

---


다시 돌아와, 그림1의 가설은 H(w) = 1*x + 0으로 나타낼 수가 있다.
여기서 그려진 선이 **각 데이터의 분포와의 차이를 계산하여 가장 적은 것이 이 모델에 적합한 선** 이라는 것을 알 수가 있다. 그래서 이를 **비용함수(Cost Function)** 이라고 부른다.

이 비용함수를 통해서 우리가 세운 가설과 실제 나타내는 값이 얼마나 다른가를 가늠해볼 수가 있다.

비용함수는 아래와 같이 나타낸다.

$$Cost(W,\quad b)\quad =\quad \frac { 1 }{ m } \sum _{ i=1 }^{ m }{ (H({ x }^{ (i) })\quad -\quad { y }^{ (i) })^{ 2 } } $$

그렇다면? 이 비용함수의 식은 어떻게 도출됐을까??
예전에 내가 아주 단순하게 설명했던 방식이 기억날 것이다.

다 우리가 중고등학교때 했던 내용이다. L2 Distance(= 유클리드 Distnce)라고도 불리는 수식이 있다. 이 수식은 아래와 같다.

$$Distance(p,q)\quad =\quad \quad d(p,q)\quad =\quad \sqrt { ({ q }_{ 1 }-p_{ 1 })^{ 2 }+({ q }_{ 2 }-p_{ 2 })^{ 2 }+\cdots +({ q }_{ n }-p_{ n })^{ 2 } } =\sqrt { \sum _{ i=1 }^{ n }{ ({ q }_{ i }-p_{ i })^{ 2 } }  }$$

어디서 많이 보던 수식아닌가? 바로 두 점 사이의 최소 거리를 구하는 방식이다.
여기서 루트만 벗기면 비용함수와 매우 흡사한 모습이란걸 알 수가 있다.

## STEP 3. 비용함수(Cost Function)

그렇다면 좀 더 깊게 들어가서 직관적으로 설명하기 위해 예시를 들어보겠다.

아래의 초록색 직선과 빨간색 직선 중 어느 모델이 더 예측을 잘할까?

![그림6](https://t1.daumcdn.net/cfile/tistory/996DB14C5B6BDA382C)


잘 모르겠다면, 아래의 문제는 어떠한가?
 
![그림7](https://t1.daumcdn.net/cfile/tistory/99379E395B6BDA3932)

많은 사람들이 왼쪽이 더 예측하기 쉽다한다 왜냐? 모든 점들이 직선 상에 존재하기 때문이다.

자, 이제 수치적으로 접근해보자. 

### STEP 3.1 오차(Error)와 제곱근 오차(Sqaure Error)


**우리가 예측하기 위해 만든 모델인 H(x) = Wx + b직선과 실제 데이터를 찍어놓은 점들의 y값의 차이를 error라 한다.**

**즉, 아래와 같이 점과 직선사이의 수직거리가 있어야 'error가 있다'라고 말할 수 있다.**

![그림8](https://t1.daumcdn.net/cfile/tistory/99E045425B6BDA3A2A)

다시 위의 그림의 왼쪽 초록색 직선을 보면 **에러가 전혀 없는 것** 을 확인할 수가 있다.그렇다면, 왼쪽 직선이 에러가 없으므로, **왼쪽 직선 모델이 더 낫다고 판단** 을 할 수가 있다.

이제 좀 더 수학적으로 계속 파고들어가보자.
error이외에 **squre error** 라는 것이 있다.

1. Error : 실제 데이터의 y값과 예측 직선모델의 y값의 차이
2. Squre Error : 실제 데이터의 y값과 예측 직선모델의 y값이 차이를 제곱해서 넓이로 보는 것.

![그림9](https://t1.daumcdn.net/cfile/tistory/99577C475B6BDA3A0C)

**Error를 제곱해서 넓이로 보는 이유가 무엇일까?**

1. 우리 눈으로 **직관적으로 볼 수가 있다.**
2. 수학적으로 볼 때, 에러가 조금이라도 있으면 **값이 증폭** 되어 큰값과 작은값의 비교를 수월하게 할 수가 있다.
3. 딥러닝 등의 알고리즘인 경사 하강법(Gradient Descent)와 오차 역전파(Backpropagation)개념에서 계산이 **용이하게 편미분** 된다.

다시 처음 문제로 돌아가 둘 중 어떤 모델이 더 나은 모델일까?

**선형회귀에서 어떤 모델이 나은지 확인하려면 Square Error 측면에서 확인해야한다.**

![그림10](https://t1.daumcdn.net/cfile/tistory/996A6D475B6BDA3B09)

Square Error를 구하고, 그것을 평균을 낸 Mean Square Error를 보면 왼쪽이 더 작다. 그러므로 왼쪽의 예측직선 모델이 예측을 잘함을 알 수가 있다.

![그림11](https://t1.daumcdn.net/cfile/tistory/9982BC335B6BDA3C24)


### STEP 3.2 비용함수(Cost Function)와 평균 제곱근 오차(Mean Sqaure Error)의 관계
이제 다시 본론으로 돌아와, 비용함수를 보자. 


아래와 같은 문제가 있다고 가정하자. 
어떤 문제인가? 관찰된 값들이 있을때(정답이 주어진 데이터가 존재), 가장 적합한 선(가장 적합한 선형모델)을 그으려면 어떻게 해야될까?

![그림12](https://t1.daumcdn.net/cfile/tistory/992744355B6BDA3D31)


위와 같은 경우 바로 **Linear Regression** 방식을 사용하는 것이다.
여기서 사용될 개념이 **최소 평균 제곱근 오차(Minimum Mean Square Error(MMSE))** 이다.

1. Error = H(x) - y : **예측값[H(X)](직선모델) - 실제값y(실제 데이터의 y값)**
2. Square Error = Error를 제곱한 값 = **(H(x) - y)^2**
3. Mean Square Error = Square Error를 다 더해서 n으로 나누어 평균낸 값 = **오차함수**

$$Cost(W,\quad b)\quad =\quad \frac { 1 }{ m } \sum _{ i=1 }^{ m }{ (H({ x }^{ (i) })\quad -\quad { y }^{ (i) })^{ 2 } } $$

이 개념(Mean Square Error : MSE)를 이용하여, **Best한 선형 모델을 그을 것이다.**

이 과정에서 사용되는 것이 **경사하강법(Gradient Descent)** 이다.
그리고 이 알고리즘을 사용하기 위해, 알아야할 개념인 **Cost Function이 Mean Square Error와 같은 것** 을 위에서 확인 할 수가 있다.
그리고 이 Cost Function을 **최소** 로 만드는 개념이 **Minimum Mean Square Error** 일 것이다. 

이제 어떻게? 비용함수를 최소로 만드는지 배워보자!

## STEP 4. 경사하강법(Gradient Descent)

정답이 주어진 데이터가 있을 때, 우리는 최적의 선형모델을 만들고 그 모델의 비용 함수를 최소로 만드는 최적의 직선을 찾아야한다. 그 비용을 최소로 하는 직선을 구하는 과정을 **학습(Train)** 이라하며, 학습에 사용되는 알고리즘이 **경사하강법** 이다.

간단한 예제로, 최초의 직선을 H(x) = Wx라 두고, 이것의 비용함수

$$Cost(W,\quad b)\quad =\quad \frac { 1 }{ m } \sum _{ i=1 }^{ m }{ (H({ x }^{ (i) })\quad -\quad { y }^{ (i) })^{ 2 } } $$

을 최소로 하는 W를 찾는 것이 목적이다. 그 과정에서 경사하강법이 어떻게 적용되는지 보자.
(머신러닝에서는 W를 θ(세타)라고 한다.)

1. 경사하강법은 비용을 최소로 만드는 예측직선 H(x) = Wx에서 **최적의 W를 업데이트하면서 찾아내는 과정** 이다.  공식으로는 아래와 같다.

$$W\quad :=\quad W\quad -\quad \alpha \frac { \partial  }{ \partial W } Cost(W)$$

W = W - ɑ* (비용함수를 W로 편미분한 것)인데, 여기서 W를 업데이트 하는 변화량 dw을 보면, ɑ와 비용의 편미분이 곱해진 것이 Gradient의 핵심이라고 할 수 있다.

1. **W** : 첫번쨰 W로서, 우리가 맨처음 초기화한 상수이다. Gradient를 태워서 비용을 최소로 만드는 W로 점점 업데이트 될 것이다.
2. **ɑ** : 학습계수(Learning Rate)로서, 학습속도를 조절하는 상수 (우리가 초기화)
3. $$\alpha \frac { \partial  }{ \partial W } Cost(W)$$ : 비용 즉, MSE를 W로 편미분한 것.

이것으로 미뤄봤을 때, 1,2 최초W와 a는 상수이므로 두고,
**3.** 비용을 편미분한 것을 통해서 W를 변화시켜 최적(비용을 최소화하는)의 W로 업데이트 시키다는 것을 확인할 수 있다. 

아래와 같이 초기화해보자 (W = 1, a = 0.01)

![그림16](https://t1.daumcdn.net/cfile/tistory/99255C345B6BDA4113)

위와 같이 W에 대한 MSE를 시각화할 수 있다.

![그림17](https://t1.daumcdn.net/cfile/tistory/995802425B6BDA423C)

이때, 그래디언트에서 W변화량이 -(음수)(a : 양수) * (위 그림의 접선의 기울기 : 양수)로 **기존 W에서 음수(-)**가 될 것이다.

※ 참고 : 비용의 편미분을 구할 때에는, 먼저 W와 비용(MSE)에 시각화 후, 접선의 기울기를 생각하자. 

![그림18](https://t1.daumcdn.net/cfile/tistory/9950674A5B6BDA4214)

이제 업데이트된 W에 대해서, 그래디언트를 통해 다음 W를 구해보자.

![그림19](https://t1.daumcdn.net/cfile/tistory/99C3DE465B6BDA4306)

구한 식에서 W = 0.98 - (0.01) * (+1) = 0.97로 조금 더 줄어들 것이다.

**이러한 그래디언트 -> W의 업데이트 과정은 언제까지 반복될까?**

2차 곡선상의 접선의 기울기(비용의 편미분량)이 거의 0 나오는 지점인 Converge까지 반복해서 W가 업데이트 된다. 이러한 W가 업데이트하는 과정에서 1번째의 H(x) = Wx직선과 200번째의 H(x) = Wx 직선을 비교해보자.

아래와 같이 그래디언트를 통해 200회 업데이트된 W로 구성된 직선은 MSE값이 매우 작아진 것을 확인할 수가 있다.

1. H(x) = Wx (W = 1, Epoch)
![그림20](https://t1.daumcdn.net/cfile/tistory/99BD39455B6BDA4418)

2. H(x) = Wx (W = Gradient 200회 업데이트한 W값, 0.5로 나왔다 가정)
![그림21](https://t1.daumcdn.net/cfile/tistory/992123455B6BDA450C)

**그러면, H(x) = Wx + b문제는 어떻게 풀 수 있을까?**
위에서 본 예제는 b=0인 문제였다. 그러나 실제에서는 b(bias)가 거의 붙어있다고 본다.

**이럴 때는, b를 새로운 W2라 보고 H(x) = W1x + W2x의 문제로 풀면된다.**

이때 W1x와 W2x의 함수로 이뤄지므로, 3차평면상에서 그려지게 된다.

![그림22](https://t1.daumcdn.net/cfile/tistory/99B49B465B6BDA4608)

이를 머신러닝으로 해결하면

![그림23](https://t1.daumcdn.net/cfile/tistory/99DB754F5B6BDA4613)

이렇게 볼 수가 있다.

## STEP 5. 학습계수(Learning Rate) ɑ는 어떻게 설정해야될까?

학습계수는 W가 업데이트되는 양을 상수로서 앞에 붙어저 조절한다. 그리고 우리가 처음에 초기화했다. 학습게수가 너무 적으면, 업데이트가 적게되므로, W들이 Converge를 찾아서 내려가는 시간이 너무 오래걸린다. 

![그림24](https://t1.daumcdn.net/cfile/tistory/99A54A425B6BDA4730)

반대로 너무 높으면 W가 Converge를 지나쳐버리기도 한다.
![그림25](https://t1.daumcdn.net/cfile/tistory/9995DD435B6BDA4707)

결과적으로 적당히 학습계수를 조절해야 비용이 0에 수렴한다.

이를 이제 아래 파이썬 코드(Tensorflow)를 보면서 재분석해보자.

```python
# X 와 Y 의 상관관계를 분석하는 기초적인 선형 회귀 모델을 만들고 실행해봅니다.
import tensorflow as tf

x_data = [1, 2, 3]
y_data = [1, 2, 3]

W = tf.Variable(tf.random_uniform([1], -1.0, 1.0))
b = tf.Variable(tf.random_uniform([1], -1.0, 1.0))

# name: 나중에 텐서보드등으로 값의 변화를 추적하거나 살펴보기 쉽게 하기 위해 이름을 붙여줍니다.
X = tf.placeholder(tf.float32, name="X")
Y = tf.placeholder(tf.float32, name="Y")
print(X)
print(Y)

# X 와 Y 의 상관 관계를 분석하기 위한 가설 수식을 작성합니다.
# y = W * x + b
# W 와 X 가 행렬이 아니므로 tf.matmul 이 아니라 기본 곱셈 기호를 사용했습니다.
hypothesis = W * X + b

# 손실 함수를 작성합니다.
# mean(h - Y)^2 : 예측값과 실제값의 거리를 비용(손실) 함수로 정합니다.
cost = tf.reduce_mean(tf.square(hypothesis - Y))
# 텐서플로우에 기본적으로 포함되어 있는 함수를 이용해 경사 하강법 최적화를 수행합니다.
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.1)
# 비용을 최소화 하는 것이 최종 목표
train_op = optimizer.minimize(cost)

# 세션을 생성하고 초기화합니다.
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())

    # 최적화를 100번 수행합니다.
    for step in range(100):
        # sess.run 을 통해 train_op 와 cost 그래프를 계산합니다.
        # 이 때, 가설 수식에 넣어야 할 실제값을 feed_dict 을 통해 전달합니다.
        _, cost_val = sess.run([train_op, cost], feed_dict={X: x_data, Y: y_data})

        print(step, cost_val, sess.run(W), sess.run(b))

    # 최적화가 완료된 모델에 테스트 값을 넣고 결과가 잘 나오는지 확인해봅니다.
    print("\n=== Test ===")
    print("X: 5, Y:", sess.run(hypothesis, feed_dict={X: 5}))
print("X: 2.5, Y:", sess.run(hypothesis, feed_dict={X: 2.5}))
```

중요한 코드만 리뷰해보자!

1. hypothesis = W * X + b (= $$H(x) = Wx + b)라는 것을 확인할 수 있을 것이다. 

2. cost = tf.reduce_mean(tf.square(hypothesis - Y))
 이 부분은 쪼개서 확인하자. 
  + 2.1 tf.square(hypothesis - Y)
        이 부분에서 hypothesis - Y = $$H(X) - Y$$ 임을 알 수가 있다. 
        여기서 tf.square는 제곱을 구하는 함수이다.
        그 후 reduce_mean은 최소 평균을 구하는 함수이다. 
        즉, 이 소스코드는 **최소 제곱근 오차(MMSE)**를 구하는 부분이라고 볼 수 있다.

3. optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.1)
  이 부분도 쪼개서 확인하자.
  + 3.1 learning_rate = 0.1 : 이 부분은 $$\alpha \frac { \partial  }{ \partial W } Cost(W)$$ 에서 알파 값을 0.1로 한다는 뜻이다. 
  + 3.2 tf.train.GradientDescentOptimizer : 이 부분은 위에서 배운 경사하강법을 적용하여 최적화를 시킨다는 내용이다. 

4. train_op = optimizer.minimize(cost) : 이 부분은 최종적으로 경사하강법을 통해 Cost 값을 최소로 하게끔 학습하겠다는 내용이다.

나머지 부분은 주석을 참고하면 될 듯하다.




