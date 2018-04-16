# CNN(Convolutional Neural Network)
CNN은 전통적인 Neural network 앞에 여러 계층의 convolutional 계층을 붙인 형태로서, 이 계층을 통해 입력 받은 이미지에 대한 특징(Feature)을 추출하게 되고, 이 특징을 기반으로 기존의 Neural network를 이용하여 분류한다.

 ![cnnex](https://user-images.githubusercontent.com/34755287/38015665-68c0c110-32a7-11e8-9fec-aa8af27ed5d6.JPG)

## Convolutional Layer
Convolutional Layer는 특징을 추출하는 역할을 한다. 이 역할을 하는 필터(Filter)와 이 필터의 값을 비선형 값으로 바꾸어 주는 Activation 함수로 이루어진다.

![convolutional layer](https://user-images.githubusercontent.com/34755287/38015738-b3645056-32a7-11e8-96ac-bc71c44bc170.JPG)

## Convolution
- What is Convolution?<br>
 Convolution은 2개의 정보가 서로 섞이는 순서가 있는 절차이다. (ex,두 벡터의 내적)
Convolution은 수학적으로 표현될 수 도 있는데, 더하기, 곱하기, 미분하기 등과 같은 수학적 연산이다.
복잡한 방적식을 간단하게 하기 위한 좋은 도구가 될 수 있다.

- 어떻게 convolution을 이미지에 적용할 수 있을까?<br>
 이미지는 2차원 배열로 생각할 수 있다. 이미지가 흑백영상이라면 2차원 배열이 하나이고, 컬러영상이라면 RGB값이 있어야 하므로, 2차원 배열이 3개가 있을 것이다. 여기서 하나의 인덱스는 이미지에서 픽셀이며 0 ~ 255 사이의 정수 값을 가진다. 이 이미지에 convolution을 적용하기 위해서는 convolution kernel이 필요하다. 이것은 실수의 단일 행렬로 이루어져 있다. 원본 이미지와 convolution kernel의 연산으로 여러가지 크기와 패턴의 이미지를 출력할 수 있다. 이 kernel의 출력으로 '피쳐 맵(Feature map)'이라고 불리는 이미지이다. kernel 하나당 각각의 색상 채널에는 각각의 피쳐 맵이 생성될 것이다.(kernel 종류가 다양할 수록 피쳐 맵도 다양하다.)

 ![convolution_kernel](https://user-images.githubusercontent.com/34755287/38008338-133cd350-3289-11e8-840b-bf36e4ae6cc6.JPG)
 > 원본 이미지와 경계선 추출 kernel을 연산하여 출력된 피쳐 맵
 (CNN은 이러한 kernel을 자동으로 만들어준다.)

위 피쳐 맵은 두 가지 정보들을 convolution을 통해 엮었다. 여기서 convolution을 적용하는 한가지 방식은 이미지 패치를 원본 이미지로부터 kernel 사이즈만큼 가져와서 이미지 패치와 convolution kernel을 연산한다. 

 ![aa-convolution-02](https://user-images.githubusercontent.com/34755287/38008637-b749abf2-328a-11e8-9933-e4637854ee45.gif)
> 하나의 연산의 합이 패쳐 맵에서 하나의 픽셀에 사상된다. 피쳐 맵의 하나의 픽셀이 계산되고 난 후에는 이미지 패치가 하나씩 오른쪽으로 이동하면서 새로운 패치가 정의되고 그 패치는 kernel과 연산하여 새로운 픽셀로 계산된다.

- 왜 이미지의 convolution이 머신러닝에서 중요한가?<br>
convolution 기술은 사용자의 필요에 따라 이미지에서 필요한 정보를 추출하거나 필요없는 정보들을 걸러내는데 주로 사용하는데, 이러한 기능때문에 kernel을 필터라고 부르기도 한다.

## Stride
위의 convolution kernel값을 계산하는 과정에서 kernel을 적용하는 간격을 Stride라고 한다.

## Padding
위의 convolution kernel을 Stride 값을 사용하여 추출한 피쳐 맵은 원본 이미지보다 작아진다. 여기서 CNN network는 여러 단계를 거쳐서 필터를 계속 적용하여 특징을 추출하는 과정인데, 이 과정에서 피쳐 맵은 계속 작아져서 처음에 비하여 특징을 유실할 가능성이 높다. 필터를 적용해가면서 특징이 유실되는 것을 기대하였다면 문제가 없지만, 충분히 특징이 추출되기 전에 결과값이 작아져 특징이 유실되면 문제가 된다. 이를 방지하지 위한 방법 중 하나가 Padding이다. Padding은 입력값 주위로 0 값을 넣어 인위적으로 크기를 키워, 결과값이 작아지는 것을 방지하는 기법이다.

![padding](https://user-images.githubusercontent.com/34755287/38016541-453230c8-32aa-11e8-8591-d2786b174a56.JPG)
> Padding은 결과값이 작이지는 것을 방지할 뿐 아니라, 오버피팅도 방지할 수 있다. (오버피팅은 원본 데이터에 0값을 넣어 원래의 특징을 희석시키고, 이것을 기반으로 머신러닝 모델이 트레이닝 값에만 정확하게 되는 현상이다.)

- CNN은 필터를 데이터에 넣고 학습을 이키면 자동으로 학습 데이터에서 학습을 통해 특징을 인식하고 필터를 만들어 낸다.

## Activation function
필터들을 통해 Feature map을 추출한 다음, 이 Feature map에 Activation function을 적용한다. Activation function은 예를 들어, 학습 데이터에서 특징이 있는지 없는지에 대해 필터를 통해 추출한 값에서 특징이 있다는 값이 6000, 없다는 값이 0이라고 나왔다고 가정하자. 이러한 값이 정량적인 값이기 때문에, 그 특징이 "있다", "없다"의 비선형 값으로 바꿔주는 과정이 필요하다. 이것을 해주는 것이 Activation function이다.
Activation function에는 시그모이드(sigmoid)함수, ReLu 함수등이 있다. 시그모이드 함수는 결과 값을 참/거짓으로 나타내는 것이 아니라 참에 가까우면 0.5 ~ 1사이에서 1에 가까운 값을, 거짓에 가까우면 0 ~ 0.5사이의 값을 리턴한다. 

![sigmoid](https://user-images.githubusercontent.com/34755287/38188200-cc13ce14-3695-11e8-861c-a81f229123c0.JPG)

하지만 CNN에서는 이 시그모이드 함수를 잘 사용하지 않고, ReLu 함수를 주로 사용한다.

![relu](https://user-images.githubusercontent.com/34755287/38188232-f7f64eb2-3695-11e8-9d42-a1e581141b4b.JPG)

ReLu함수를 사용하는 이유는 CNN에서 신경망의 깊이가 깊어질수록 학습이 어렵기 때문에 전체 레이어를 한번 계산한 후, 그 계산값을 재활용하여 다시 계산하는 Back propagation이라는 방법을 사용한다. 만약 시그모이드 함수를 activation 함수로 사용할 경우, 레이어가 깊어 지면 이 Back propagation이 Gradient Vanishing현상때문에 제대로 작동하지 않는다. Gradient Vanishing 현상은 값을 뒤에서 앞으로 전달할 때 희석되는 현상이다. 이와 같은 이유로 시그모이드 함수보다는 ReLu함수를 사용한다.

## 풀링(Sub sampling or Pooling)
Convolutional layer를 거쳐 추출된 특징들은 필요에 따라 Sub sampling이라는 과정을 거친다. 어느정도 특징이 추출되면, 이 모든 특징을 가지고 판단을 할 필요가 없다. 그래서 추출된 Activation map을 인위로 줄이는 과정을 거치는데, 이 작업을 Sub sampling 또는 Pooling이라고 한다. Sub sampling은 여러가지 방법이 있는데, Max pooling, Average pooling, L2-norm pooling 등이 있다. 그 중에서 Max pooling 기법을 가장 많이 사용한다.

### Max pooling
Max pooling은 Activation map을 M x N의 크기로 자른 후, 그 안에서 가장 큰 값을 추출하는 방법이다. 아래 그림을 살펴보면 4x4 Activation map에서 2x2 max pooling filter를 stride 2로 설정하여 2칸씩 이동하면서 max pooling을 하는 상황이다. 좌측 상단 윈도우에서 6이 가장 큰 값이므로 6을 추출하고, 이러한 과정을 반복하여 오른쪽과 같은 윈도우를 만든다.

![maxpooling](https://user-images.githubusercontent.com/34755287/38189605-eeb57a5c-369b-11e8-8821-f091b93bf3bf.JPG)

Max pooling은 해당 범위의 특징 값중 가장 큰 값이 다른 특징들을 대표한다는 개념을 기반으로 하고 있다. 여기서 주의할 점은 Pooling은 Activation 함수마다 매번 적용하는 것이 아니라, 데이터의 크기를 줄이고 싶을때 선택적으로 사용하는 것이다. 이와 같은 Sampling을 통해 얻을 수 잇는 장점은 크게 두가지 이다. 첫 째, 전체 데이터의 크기가 줄어들기 때문에 연산에 소모되는 컴퓨팅 리소스가 적어진다. 둘 째, 데이터의 크기를 줄이면서 소실이 발생하기 때문에, 오버피팅을 방지할 수 있다.

아래 그림은 여러개의 Convolutional filter(CONV), Activation function(RELU), Pooling(POOL)가 사용되는 과정을 볼 수 있다.

![cvl](https://user-images.githubusercontent.com/34755287/38189811-9a31028e-369c-11e8-9448-0902872b1459.JPG)

## Fully Connected Layer
Convolutional Layer에서 특징이 추출되었다면 이 추출된 특징 값을 기존의 뉴럴 네트워크(인공 신경 지능망)에 넣어 분류를 한다. 아래의 그림은 CNN의 최종 네트워크 모양이다.

![cnnnetwork](https://user-images.githubusercontent.com/34755287/38189944-1bd0ba50-369d-11e8-952f-7ebaba1fb854.JPG)

### Softmax 함수
Softmax함수는 위의 CNN 네트워크에서 가장 마지막에 표현되어 있다. Softmax함수 또한 Sigmoid나 ReLu함수과 같은 Activation function중 하나이다. Sigmoid함수가 이산 분류(결과값에 따라 참 또는 거짓으로 분류)함수라면, Softmax함수는 여러개의 분류를 가질 수 있는 함수이다. 아래 그림은 Softmax함수이다.

![softmax](https://user-images.githubusercontent.com/34755287/38190045-7e080084-369d-11e8-9730-557a01dd9f7b.JPG)

P3(x)는 특징(feature) x에 대해 P3일 확률, P1(x)는 특징x에 대해 P1인 확률이다. Pn값은 항상 0 ~ 1.0의 범위를 가지며, P1 + P2 + ... + Pn = 1이 성립한다.
예를 들어, 어떤 사람 이미지를 넣었을 때, 설현일 확률 0.9, 수지일 확률 0.1 이와 같은 식으로 표현된다.

### Dropout Layer
Dropout은 오버피딩(over-fit)을 막기 위한 방법으로 뉴럴 네트워크가 학습중일 때, 랜덤하게 뉴런을 아웃시켜 학습을 방해한다. 이와 같은 방식으로 현재 학습이 학습용 데이터에 치우치는 현상을 막아준다.

![dropout](https://user-images.githubusercontent.com/34755287/38190217-3adeb1f8-369e-11e8-92a2-58c497b3b54c.JPG)

일반적으로 CNN에서는 Dropout layer를 Fully connected network 뒤에 위치시키지만, 상황에 따라 Max pooling 계층 뒤에 위치시키기도 한다. 다음 그림은 Dropout을 적용하고 학습시킨 모델과 적용하지 않은 모델 사이의 예측 정확도를 비교한 결과이다.

![dropoutres](https://user-images.githubusercontent.com/34755287/38190283-89d30386-369e-11e8-81c9-fdc99019078f.JPG)
