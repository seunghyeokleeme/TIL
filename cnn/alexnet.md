# AlexNet

> **ImageNet Classification with Deep Convolutional Neural Networks**  
> Alex Krizhevsky, Ilya Sutskever, Geoffrey E. Hinton (2012)

![](/assets/img/alexnet.png)

AlexNet은 대규모 데이터인 ImageNet를 사용하여 분류를 위해 깊은 CNN을 훈련

해당 시기에는 소규모 데이터셋 위주였으며, 레이블을 하는데 상당한 비용이 들었으나, ImageNet이라는 대규모 데이터가 생기면서 대규모 데이터에 대해서 이미지 분류를 하기 위한 필요성이 높아졌다.

10개 클래스가 아닌 수많은 클래스 다중 분류를 하기 위해서는 모델의 복잡성이 올라가고 데이터셋의 크기로만 해결이 되지 않았는데 CNN 네트워크를 사용하여 해결하였다.

고 해상도의 이미지를 적용하기 위해서는 GPU 비용이 크므로 해당 모델에서는 GPU 2개를 갖고 병렬적으로 처리

모델의 사이즈를 키우면서 오버피팅 문제가 발생하였다. 해당 논문에서는 데이터 증강 기법, 드롭아웃 기법을 적용하였다.

그리고 해당 논문에서 전통적인 CNN 구조에서 변형하여 activation function으로는 ReLU를 사용했고 pooling layer에서는 no-overlapping이 아니라 overlapping를 사용하였다.


