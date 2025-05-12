# Gradient descent

딥러닝을 공부하면서 고등학교 수학 지식이 들어가는 파트는 개인적으로 Gradient descent 파트라고 생각한다. 물론 해당 파트 외에도 수학적 지식이 필요하다.

물론 손으로 풀고 외우는 이론적 수학이 아니라 공학 수학이 필요하다. 특히 엔지니어로서는 자신이 가장 잘 다룰 수 있는 도구로 수학을 해결하면 된다고 개인적으로 생각한다.

예를 들어 손으로 수식을 못 풀어도 프로그래밍으로 수학 문제를 풀 수 있다면 그 정도라면 충분하다고 생각한다. 물론 이론적인 수학 내용이 프로그래밍을 하기전에 새로운 관점을 보여주고 넓은 시야를 보여준다는 장점이 있다.

DL에서 Gradient descent이라는 단어를 많이 봤을 것이다.

앞서 우리는 loss function에 대해서 알아보았다.

loss function을 설정하였으면 데이터를 기반으로 loss를 줄이도록 최적의 파라미터를 찾아야 한다. 

최적의 파라미터를 어떻게 찾을 것인가? 이러한 문제를 해결하기 위해 GD (Gradient descent) 개념이 등장하였다.

GD의 개념은 생각보다 간단하다.

1. parameter를 임의 값으로 초기화한다. 
2. 현재 위치에서 그래디언트 방향을 찾는다.
3. 그래디언트의 반대 방향으로 조금 움직인다. (움직이는 값의 정도를 설정해야 한다.)
4. 새로운 위치에서 2번 - 3번 과정을 계속 반복한다.
5. 최소점에 도달할 때 까지 반복한다.

현재 위치에서 그래디언트의 반대 방향으로 움직이면서 찾는것이 처음부터 최적의 파라미터의 값을 구하는 것보다 쉽고 빠르다.

여기서 그래디언트는 항상 가장 가파르게 증가하는 방향을 가리킨다.

loss를 줄이기 위해서는 최소점을 찾아야하니 그래디언트의 반대 방향으로 움직여야 한다는 것이 당연하다고 볼 수 있다.

매번 textbook으로 보면 항상 나오는 그래프와 식이 있다.

$$
{\displaystyle \mathbf {p} _{n+1}=\mathbf {p} _{n}-\gamma \nabla F(\mathbf {p} _{n})}
$$

위의 식을 보면 ${p}_{n}$ 은 현재 위치에서의 parameter 값이며, $\gamma$는 조금 움직이는 (Learning rate) 역할을 한다. 그리고 나머지 수식을 보면 그래디언트의 반대 방향이라는 표시도 확인 할수 있다.

그리고 그래프를 설명하면서 learning rate의 중요성을 설명하는 내용들은 익숙할 것이다. 

learning rate가 너무 높으면 그래디언트의 크기가 너무 커서 최소점을 지나쳐서 발산하거나 불안정한 학습을 하며, 너무 작으면 최소점을 도달할 때 까지 엄청 느리다고 설명한다.

이러한 내용을 그래프나 영상을 통해 설명하는데 프로그래밍을 해서 정말 그렇게 동작하는지 확인해보자.

## Gradient descent의 Learning Rate 조정에 따른 실험 구현

먼저 textbook에서 흔히 설명하는 $f(x, y) = x^2 + y^2 $의 함수로 그래프를 그려보자. learning rate도 1, 0.1, 0.01, 0.001로 설정하자.

먼저 수식을 그래프로 설명하려면 제일 간단한 Python을 이용하여 matplotlib 라이브러리를 사용하는것이 편하다.

앞으로 수식이나 그래프를 표현할때 matplotlib 라이브러리 사용법은 아주 능숙하게 익혀둔다면 많이 편할 것이다.

Gradient descent를 수행하는 코드에 대해서는 자세히 설명하지 않을 것이다. 

> 파이썬과 기초적인 수학 내용을 알면 금방 구현이 되기 때문이다. 그리고 코드 구현이 힘들다면 하루 뒤에 다시 도전하기를 바란다. (개인적으로 엔지니어는 무슨 방법을 쓰든 구현을 해야한다고 생각한다. 구현이 되지 않으면 그건 상상 코딩이고 남에게 증명할 수 없다.)

> 개인적인 경험으로는 도메인 지식은 탄탄한데 소프트웨어 구현을 못해서 다른 사람에게 맡기는 경우를 많이 봤다. 이런 경우에 만약 내가 구현을 할수 있다면, 생각보다 적은 노력으로 타 분야에게 도움을 줄수 있고 기여를 할 수 있다.

<table>
  <tr>
    <td>
      <img src="https://raw.githubusercontent.com/seunghyeokleeme/dl-note/main/gd/gradient_descent_1.gif"
           alt="Learning Rate: 1.0" width="400"/>
    </td>
    <td>
      <img src="https://raw.githubusercontent.com/seunghyeokleeme/dl-note/main/gd/gradient_descent.gif"
           alt="Learning Rate: 0.1" width="400"/>
    </td>
  </tr>
  <tr>
    <td>
      <img src="https://raw.githubusercontent.com/seunghyeokleeme/dl-note/main/gd/gradient_descent_3.gif"
           alt="Learning Rate: 0.01" width="400"/>
    </td>
    <td>
      <img src="https://raw.githubusercontent.com/seunghyeokleeme/dl-note/main/gd/gradient_descent_2.gif"
           alt="Learning Rate: 0.001" width="400"/>
    </td>
  </tr>
</table>

[구현 코드](https://github.com/seunghyeokleeme/dl-note/blob/main/gd/gd_animation.py)

해당 그래프를 보면 학습률에 따라 수렴 속도를 확인할 수 있으며 Gradient descent 실험이 제대로 동작하다는 것을 확인 할 수 있다.

- lr = 1.0: 한 번의 스텝만으로도 너무 크게 이동하여 원점을 지나친 후 발산하는 모습을 보인다. 너무 큰 학습률은 안정적인 수렴을 방해한다.
- lr = 0.1: 몇 차례 반복만에 원점에 거의 수렴하여 안정적으로 작동함을 확인할 수 있다.
- lr = 0.01: 학습률이 낮아 수렴은 하지만 50회 반복 내에 원점까지 이르기에는 속도가 느리다.
- lr = 0.001: 더 작은 학습률일수록 수렴 속도가 더욱 느려져, 같은 횟수 반복으로는 충분한 수렴을 이루지 못한다.

## GD (Gradient descent) 한계

1. GD를 계산할때 모든 데이터를 고려하여 그래디언트의 값을 계산하기 때문에 한 번 업데이트를 하는 과정 또한 시간이 많이 걸린다.

2. Global Minimum이 아닌 Local Minimum에 빠질 수 있다.

GD의 계산 과정을 보면 현재 위치마다 gradient를 구해서, 즉 반대방향으로 움직이어서 현재 위치에서 가장 가까운 Minimum을 찾기 때문에 Global Minimum이 아닌 확률이 매우 높다.

특히 딥러닝에서는 복잡한 모델을 사용하고, loss function 또한 더 많은 Local Minimum을 가지므로 무작위 위치에서 시작하여 가장 가까운 Local Minimum으로 수렴하므로 해당 지점이 Global Minimum일 확률은 거의 희박하다.

이러한 문제를 해결하기 위해 개발된 기법이 Stochastic Gradient descent의 기법이다. (물론 SGD를 사용한다고 Global Minimum에 수렴한다고 보장할 수 없다...)