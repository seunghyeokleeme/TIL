# Linear regression and Loss function

Linear regression은 머신러닝에 대해서 기본적으로 푸는 문제이다.

먼저, regression(Regression analysis)은 input과 output 사이의 관계를 추정하는 것이다.

Regression analysis에는 여러 방법론이 있는데 Linear prediction function을 사용해 분석하는 것을 Linear regression 이라고 한다.

퍼셉트론에서는 일차 함수를 활용하여 데이터를 분류를 하였다. \(y = ax + b\)

이때 x는 입력값이며, y는 출력값, a와 b는 weight, bias이다.

퍼셉트론 구현 예제에서는 weight 와 bias를 사람이 정하여 선형 함수를 모델링하였다.

xor와 같이 선형함수 간의 관계가 복잡해지거나 패턴을 정확히 구분하려면 주어진 데이터를 통해 weight, bias 즉 최적의 파라미터를 찾을 수 있으며, 이는 최적의 선형 모델을 만들 수 있다.

이때 방대한 데이터를 통해 최적의 선형 모델간의 관계를 찾을때는 사람이 집적 parameter를 정하는 것이 아니라 머신러닝이 수행한다.

## Loss function

linear regression을 풀때는 weight, bias의 값을 AI가 방대한 데이터를 통해 최적의 값을 도출하는 것을 하여 이상적인 선형 함수의 관계를 나타내는 것입니다.

예를 선형 함수를 다음과 같은 일차 함수라고 가정합시다. 이때 a와 b는 weight과 bias 입니다.

먼저 AI가 최적의 값들을 도출하는 학습 초기 단계에서는 임의로 a, b를 정합니다.

아래의 그림과 같은 그래프를 나타냅니다.

![](https://github.com/seunghyeokleeme/dl-note/blob/main/assest/linear_regression.png?raw=true)

초록색 선은 임의로 정한 선형 함수이며 빨간색 선은 오차입니다. 이때 임의로 정한 선형 함수의 예측값과 실제 값의 차이입니다.

지금 현재 그래프는 무작위로 설정한 초기 파라미터로 그린 그래프이므로 오차를 최소화해야합니다.

오차를 최소화를 하면 해당 파라미터가 최적의 값을 도출하여 최적의 선형 함수를 찾을 수 있습니다.

현재 파라미터가 얼마나 안 좋은지를 나타내는 함수를 Loss function이라고 부릅니다.

AI는 학습을 진행하면 Loss function, loss의 값을 최대한 줄이도록 훈련해야합니다. 즉 선형함수의 출력값이 실제 출력과 가깝게 나오도록 loss를 줄이도록 설계해야합니다.

## MSE and MAE Loss

Loss function을 설계할 때 문제의 특성에 맞게 정의 해야합니다. 

예를 들어 실제 출력값을 $y$, 예측 값을 $\hat{y}$로 설정 하여 Loss를 정의하겠습니다. error는 $e_i = y_i - \hat{y_i}$ 입니다.

전체 데이터를 고려하려 Loss를 정의하는 방법 중 2가지를 소개하겠습니다.

- 1. MSE Loss (Mean squared error)

$$\mathrm{MSE} = \frac{1}{n}\sum_{i=1}^n (Y_i - \hat Y_i)^2={\frac{1}{n}\sum_{i=1}^n e_{i}^2}$$

- 2. MAE Loss (Mean absolute error)

$$
\mathrm{MAE} = \frac{1}{n}\sum_{i=1}^n \left| Y_i - \hat Y_i\right|={\frac{1}{n}\sum_{i=1}^n \left| e_{i} \right|}
$$

Loss function은 문제의 특성에 맞게 정의하는 것이 매우 중요합니다. 그러므로 데이터 하나($e_3$)를 특정하여 $e_3^2$와 $\left|e_3\right|$의 그래프로 비교하여 설명하겠습니다.

![](https://github.com/seunghyeokleeme/dl-note/blob/main/assest/mse_loss.png?raw=true)

![](https://github.com/seunghyeokleeme/dl-note/blob/main/assest/mae_loss.png?raw=true)

위의 두 그래프를 살펴보면 MSE가 MAE보다 a의 변화에 error가 민감하게 반응합니다.

즉, MSE는 MAE보다 parameter 변화에 민감하며, 전체적인 트랜드보다 큰 오차에 대해서 더 민감하게 반응하여 이상치에 더 가깝게 지내는 예측선을 찾게 됩니다.

반면 MAE는 MSE보다 이상치의 영향을 상대적으로 적게 받습니다. 

그러므로 데이터의 특성과 분석에 맞는 Loss를 찾는 것이 중요합니다.

데이터 특성과 분석 목적에 맞게 Loss function을 설정하였다면 이제 loss를 줄이는 최적의 parameter를 찾아야합니다. 

앞에서 이야기했듯이 parameter가 복잡해지면 일일이 손으로 계산할수 없으며 긴 시간이 걸립니다.

그러므로 이러한 문제점을 해결하기 위해 Gradient Descent라는 알고리듬이 생성되었습니다.
