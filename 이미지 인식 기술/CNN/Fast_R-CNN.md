# Fast R-CNN
Fast R-CNN은 논문의 제목에서도 알 수 있듯이, R-CNN의 단점인 속도를 개선하기 위한 연구이다. 

![festrcnn](https://user-images.githubusercontent.com/34755287/38194915-94b44f1e-36b5-11e8-8d2a-7f5bad244a73.JPG)

그렇다면, R-CNN과 Fast R-CNN을 비교한 그림을 보자.

![festrcnn2](https://user-images.githubusercontent.com/34755287/38194930-ab0c377c-36b5-11e8-9528-9a80c41d86e5.JPG)

Fast R-CNN에서 개선된 점을 살펴보자.
1. R-CNN에서 임의의 region이 주어졌을 때, classification을 수행하기 위해 필요한 feature 추출하는 과정 중 하나인 Cropping 하는 과정을 image level이 아닌 feature map level에서 수행하여, 2,000번의 CNN연산에서 1번의 CNN 연산으로 줄였다.
2. Classification을 수행하기 위해서는 feature크기가 모두 동일해야 한다. R-CNN에서는 다른 크기의 region들을 모두 warping 과정을 통해 같은 크기로 변환하였는데, 이는 크기를 줄이고 ratio를 변경하는 과정에서 정보의 손실이 일어난다. 이를 보완하기 위해 Fast R-CNN에서는 **Spatial Pyramid Pooling, ROI(Region Of Interest) Pooling** 방식을 사용한다.

## Spatial Pyramid Pooling, ROI Pooling

### Spatial Pyramid Pooling(SPP) 개념

![ssp](https://user-images.githubusercontent.com/34755287/38195327-fa8a6e02-36b7-11e8-9f96-3124a3bd2496.JPG)

다양한 크기의 입력으로 부터 일정한 크기의 feature를 추출하는 방법 중 Bag-of-words(BoW)라는 방법이 있다. 하지만 BoW는 이미지가 지닌 특징들의 위치 정보를 모두 잃어버린다. 이를 보완하기 위한 Spatial Pyramid Pooling 은 이미지를 여러개의 일정 개수의 지역으로 나눈 뒤, 각 지역에 BoW를 적용하여 지역적인 정보를 어느정도 유지할 수 있다.

### SPP/ROI pooling
SPPNet은 Spatial Pyramid Pooling 기법을 CNN feature map에 적용한 것이다. SPPNet에서 SPP layer는 feature map 상의 특정 영역에 대해 고정된 개수로 영역을 나눈 뒤, 각 영역에 max pooling 또는 average pooling을 적용하여 고정된 길이의 feature vector를 구할 수 있다. Fast R-CNN에서는 SPP layer의 single level pyramid만을 사용하며, 이를 ROI Pooling layer라고 한다.

![rolpooling](https://user-images.githubusercontent.com/34755287/38195601-3ba4f05a-36b9-11e8-85a4-58ea911ba169.JPG)

여기서 주의할 점은 SPP를 수행할 feature map의 크기가 a x a 이고, 이를 n x n 개의 영역으로 나눈다고 했을 때, 각 영역의 window size가 정수배가 되지 않는 경우가 존재한다. (예를 들어, 13 x 13, 6 x 6일 경우, window size는 2.167이 된다.) 이러한 misalignment를 해결하기 위해 window size는 올림, pooling을 위한 stride는 내림을 하도록 정의하였다. 여기서 실제 영역 좌표값과 약간의 차이가 발생하기 때문에 정확한 pixel-level의 feature가 필요한 상황해서는 문제가 될 수 있다.

마지막으로 Fast R-CNN은 약간의 속도 개선과 training의 복잡함의 문제를 해결하였지만, 여전히 몇가지 문제점이 있다.
1. Region proposal 계산이 neural network 외부에서 일어난다.
2. Region proposal 계산(Selective search)이 전체 성능의 bottleneck이 된다. (Selective search가 느린 이유 중 하나는 CPU환경에서 계산되기 때문이다.)
