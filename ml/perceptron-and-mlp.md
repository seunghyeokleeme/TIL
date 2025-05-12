# Perceptron & MLP

ANN(Artificial Neural Network)를 처음 배울때는 Perceptron에 대해서 먼저 배울 것입니다.

퍼셉트론(perceptron)은 인공신경망의 한 종류로서, 1957년에 코넬 항공 연구소(Cornell Aeronautical Lab)의 프랑크 로젠블라트 (Frank Rosenblatt)에 의해 고안되었습니다.

![](https://www.tutorialspoint.com/tensorflow/images/single_layer_perceptron.jpg)


기본적인 Single Perceptron은 다음과 같습니다.

두개의 Input Node와 1개의 Output Node로 구성 되어 있습니다.

Input과 Output 사이에는 edge가 존재하며 각 edge에는 weights를 가지고 있습니다.그리고 Output Node 안에는 Activation Function이 존재합니다.

이때 Activation Function에는 다양한 function을 사용할 수 있으나, 기본적으로 Step Unit Function을 사용합니다.

예를 들어 Perceptron의 구조를 살펴 봅시다.

X1, X2라는 Input Node가 존재하며 Y라는 Output Node가 존재합니다.

Input과 Output Node 사이에는 edge가 존재하니 W1과 W2라는 weight가 있습니다.

Input Node에서 신호를 보낼때는 각각 고유한 weights를 곱합니다.

Output Node에서는 weights과 곱한 Input Signal의 sum이 정해진 임계값을 넘을 때만 1을 출력합니다. 이를 $\theta$ 기호로 나타냅니다.

해당 내용을 수식으로 표현하면 다음과 같습니다.

$$
y =
\begin{cases}
1, & \text{if } w_{1}x_{1} + w_{2}x_{2} > \theta,\\
0, & \text{otherwise}.
\end{cases}
$$

Perceptron은 Input Signal에 각각 unique한 weights을 부여합니다. weights는 각 Input이 Output에 영향력을 조절하는 요소로 작동합니다.

weights가 클수록 해당 신호가 Output에 영향력을 더 가집니다.

## Perceptron으로 Logic Gate 구현

Perceptron을 이용하여 기본적인 logic gate를 구현 해봅시다.

먼저 OR Gate를 구현합시다.

OR Gate의 Truth table은 다음과 같습니다.

|  x1  | x2  | Output  |
|------|-----|---------|
| 0 | 0| 0|
| 1 | 0| 1|
| 0 | 1| 1|
| 1 | 1| 1|

그러면 OR Gate를 코드를 통해 구현합시다. ($w_1, w_2, \theta$)

![or gate](https://github.com/seunghyeokleeme/dl-note/blob/main/assest/or_gate.png?raw=true)

```python
import matplotlib.pyplot as plt
import numpy as np

def OR_Gate(x1:int, x2:int) -> int:
    w1, w2, theta = 0.5, 0.5, 0.3
    temp = x1*w1 + x2*w2
    if temp > theta:
        return 1
    else:
        return 0

# OR 게이트 입력 데이터 생성
x = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
y = np.array([OR_Gate(x[i][0], x[i][1]) for i in range(len(x))])

# 시각화
plt.figure(figsize=(8, 6))

# 데이터 포인트 그리기
for i in range(len(x)):
    if y[i] == 0:
        plt.plot(x[i][0], x[i][1], 'ro', markersize=10, label='0' if i == 0 else "")
    else:
        plt.plot(x[i][0], x[i][1], 'bo', markersize=10, label='1' if i == 1 else "")

# 결정 경계선 그리기
w1, w2, theta = 0.5, 0.5, 0.3
x_line = np.linspace(-0.5, 1.5, 100)
y_line = (theta - w1 * x_line) / w2
y_line_2 = (0.6 - w1 * x_line) / w2

plt.plot(x_line, y_line, 'g-', label='Decision Boundary')
plt.plot(x_line, y_line_2, 'r-', label='Wrong Decision Boundary')

# 그래프 꾸미기
plt.grid(True)
plt.xlim(-0.1, 1.1)
plt.ylim(-0.1, 1.1)
plt.xlabel('x1')
plt.ylabel('x2')
plt.title('OR Gate Visualization (w1 = 0.5, w2 = 0.5, theta = 0.3)')
plt.legend()
plt.show()
```

위의 코드를 통해 $w_1, w_2, \theta$ 를 직접 정하면 OR Gate를 구할수 있습니다.

마찬가지로 AND Gate 또한 구현할 수 있습니다.

![and gate](https://github.com/seunghyeokleeme/dl-note/blob/main/assest/and_gate.png?raw=true)

$\theta$는 일반적으로 bias($b$)으로 표시할수 있습니다. $(\theta = -b)$

$$
y =
\begin{cases}
1, & \text{if } w_{1}x_{1} + w_{2}x_{2} + b > 0,\\
0, & \text{otherwise}.
\end{cases}
$$

$w_1, w_2$는 weight이며, $b$는 bias를 나타냅니다.

$b$는 들어오는 모든 입력 뉴런 신호의 값들의 총합에서 활성화 정도를 정하는 민감도를 조절하는 parameter입니다.

## Single Perceptron의 한계

![xor gate](https://github.com/seunghyeokleeme/dl-note/blob/main/assest/xor_gate.png?raw=true)

Single Perceptron으로는 XOR Gate를 표현할 수 없습니다. 그래서 Perceptron으로 2층 구조로 사용하여 XOR Gate를 표현할 수 있습니다.

즉, Single Perceptron으로 표현하지 못하는 것을 층 하나 늘려 구현할 수 있습니다. 이처럼 다양한 구조를 Multiple Perceptron으로 표현할 수 있습니다.

## MLP

![mlp](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*k_qarynPR-7Lb-jVUpq_Qw.png)
> https://medium.com/@nlunge786/a-deep-architecture-multi-layer-perceptron-164bc5ff3842

MLP는 다층 Perceptron을 구성합니다. 즉, Node끼리 모두 연결된 층을 Fully-Connected layer라고 합니다.

Fully-Connected layer

해당 hidden 레이어는 총 2층 구조로 이루어져 있습니다.

- 첫 번째 히든 레이어: weights: $4 \times 4 = 16개$, bias: 4개
- 두 번째 히든 레이어: weights: $4 \times 4 = 16개$, bias: 4개