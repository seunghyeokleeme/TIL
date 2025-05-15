# Mini-Batch Gradient descent

Mini-Batch Gradient descent는 SGD의 한계를 보완하기 위해 개발 된 기법이다.

[SGD](/ml/stochatic-gradient-descent.md) 는 단 하나의 데이터만 고려하기 때문에 대규모 데이터셋에서 편향된 업데이트를 발생한다.

그래서 loss를 계산시에 하나의 데이터가 아닌 복수의 데이터를 포함하여 계산한다.

그리고 Mini-Batch Gradient descent는 SGD와 [GD](/ml/gradient-descent.md)가 될수 있다.

Batch Size가 1이면 SGD가 되며, Batch Size가 전체 데이터 개수로 설정하면 GD가 된다.

Batch Size는 하이퍼파라미터이므로 유연하게 조정하면서 사용할 수 있다.

그리고 GPU를 사용하면 병렬 연산이 가능하므로 Batch의 효율성이 높아진다.

GPU를 사용하면서 Batch Size를 키우면 학습 속도를 향상 시킬수 있다.

그러나 Batch Size를 키우는 것이 항상 좋은 것이 아니다. 왜냐하면, Batch Size가 커질수록 GD에 가깝게 동작하며, local Minimum에 수렴할 가능성이 높아진다.

그러므로 Batch Size를 선택시에 Trade-Off를 고려해야한다.
    
- 학습 속도
- 최적화 (Global Minimum에 도달 가능성)

그리고 앞서 설명한 GD, SGD, Mini-Batch Gradient descent 기법 모두 현재 시점에서 그래디언트의 정보를 활용하여 업데이트 한다.

이 알고리즘을 개선하기 위해서 과거 그래디언트의 정보도 활용하는 기법들이 등장하기 시작했다. (Momentum, RMSProp, Adam)