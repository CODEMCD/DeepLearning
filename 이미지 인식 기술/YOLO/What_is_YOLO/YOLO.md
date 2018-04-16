# YOLO (You Only Look Once)

## 개요
기존의 방식이었던 R-CNN계열은 real time 환경에서 이용하기에는 속도면에서 부적합하다. Fast R-CNN은 0.5프레임, Faster R-CNN도 7프레임이 최대이다. YOLO는 처음 발표할 때 45프레임정도를 보여주었고 점점 더 발전하고 있다.

## R-CNN과 차이
R-CNN 계열에서 가장 시간이 오래 걸리는 과정은 Region proposal을 하는 과정이다. Faster R-CNN의 학습과정에서 2,000개의 proposal과 테스트 과정에서 300개의 proposal가 필요하다. 이 proposal 개수가 많고, 이것을 계산하는 과정에서 오버헤드가 크기 때문에 시간이 오래 걸린다. 이미지에서 object의 bounding box를 찾는 방법은 크게 2가지이다. Proposal 방식과 Grid 방식이다. 아래 그림은 이 2가지 방식을 비교한 것이다.

![rcnnvsyolo](https://user-images.githubusercontent.com/34755287/38225785-82b0a1a6-3731-11e8-9d39-17b62dfb481c.JPG)

그림에서 볼 수 있듯이, Proposal방식의 Selective search는 2.24초, EdgeBoxes방식은 0.38초가 소요된다. 그리고 두 그림의 proposal(노란색 박스)의 개수를 보면 많다는 것을 볼 수있다. 하지만 오른쪽 그림의 Grid방식에서 grid cell(초록색 박스)의 개수가 곧 proposal의 개수이며, 이 수가 왼쪽보다 훨씬 적은 것을 알 수 있다. 즉 이 proposal을 구하는 과정에서 오버헤드가 없어, 속도면에서 큰 차이를 낼 수 있다.

YOLO는 real time을 확보하기 위해 Grid방식을 더욱 발전시켜 적용하였다.

## YOLO Network

YOLO 네트워크는 최종 출력에서 경계박스 위치와 클래스분류가 동시에 이루어진다. 하나의 네트워크가 특징 추출, 경계박스 생성, 클래스 분류를 처리하기 떄문에 간단하고 빠르다.

![yolo1](https://user-images.githubusercontent.com/34755287/38227399-fa25659e-3738-11e8-80bd-443ec3340ca6.JPG)

위의 그림은 좌측의 영상이 입력되면 중앙의 2개의 그림으로 결과를 얻는다. 오른쪽의 그림은 네트워크의 결과를 이용해서 네트워크 외부에서 처리한 영상이다. <br>
 입력 영상부터 살펴보면, 입력 영상을 7 x 7 그리드로 나눈다. 각 grid에서 중심을 grid 안쪽으로 하며, 크기가 일정하지 않은 경계박스를 2개씩 생성한다. Grid cell 개수가 7 * 7 = 49개이므로, 총 경계박스 개수는 98개가 만들어진다. 이 경계박스 안에 어떠한 오브젝트가 있을 것이라고 확신(Confidence score)할수록 박스를 굵게 그린다.(그림 중간 위) 그리고 대체로 얇은 경계 박스들은 안에 오브젝트가 없다고 판단하고 지운다. 이 과정을 지나고 남은 후보 경계박스들은 NMS(Nom - Maximal suppression : 비-최대값 억제)알고리즘을 이용해 선별하면 우측의 이미지에서 볼 수 있듯이, 3개의 결과 박스만 남는다. <br>
 그림 중간 아래는 grid마다 색깔이 구분되어져 있다. 이 색깔은 클래스를 의미한다. 각 grid cell은 해당 영역에서 제안(proposal)한 경계 박스안의 오브젝트가 어떤 클래스인지 색깔을 이용해 표현한다. 결과적으로 우측 영상에서 남은 3개의 경계박스가 어떤 클래스인지 알 수 있다.

![yolonetwork](https://user-images.githubusercontent.com/34755287/38227812-03c21e92-373b-11e8-8631-25855ee46ea9.JPG)

위 그림은 YOLO 네트워크 구조를 나타낸 것이다. 위 구조는 GoogleLeNet을 약간 변형하여 특징 추출기로 사용하였다. 이후 4번의 convolutional layer와 2번의 fully connected layer 과정을 거쳐 7 x 7 x 30 사이즈의 결과를 추출한다. 이 결과 데이터안에 예측한 결과의 경계박스와 클래스 정보등이 포함되어있다.

### YOLO network 과정
위의 입력 영상을 통해 YOLO 네트워크가 동작하는 방식에 대해 자세히 알아보자.

![yoloprocess1](https://user-images.githubusercontent.com/34755287/38227972-cf3961e8-373b-11e8-9261-c09be316c54c.JPG)

왼쪽 빨간네모점은 확대해보면 중간의 입력영상의 빨간색 grid cell 박스에서 볼 수 있듯이, 개의 중앙 부분에 해당한다. 그리고 그 박스보다 큰 노란색박스는 빨간색 grid cell에서 예측한 경계 박스이다.

오른쪽의 30개의 채널은 (경계 박스의 정보 4개, 경계 박스안에 오브젝트가 있을 확률(Confidence) 1개) * 2, 어떤 클래스일 확률 20개로ㅗ 구성된다. 
- 경계 박스 정보 x, y : 노란색 경계 박스의 중심이 빨간색 grid cell 박스의 중심에서 어디에 있는가?
- 경계 박스 정보 w, h : 노란색 경계 박스의 width, height가 전체 이미지 크기에 대해 상대적으로 어느 정도 크기를 갖는가?
예를 들어, 위 그림에서 x, y는 각각 0.5정도, w, h는 각각 2/7, 4/7정도가 된다. 

노란색 경계 박스는 반드시 그 중심이 빨간색 grid cell 안에 있어야 하며, width와 height는 빨간색 grid cell보다 작을 수도 있고 위의 그림에서 처럼 클 수도 있다. 노란색 경계 박스가 ROI, 즉 오브젝트 후보이며, 이 박스를 한 grid cell마다 2개씩 만든다.
 
![yoloprocess2](https://user-images.githubusercontent.com/34755287/38285442-20bcc392-37fb-11e8-943d-a41a773f32cc.JPG)

- 위 그림은 2번째 경계 박스를 만든 모습이고, 오른쪽의 채널을 보면 5칸은 위의 첫번째 경계 박스에 대한 정보, 다음 5칸은 위의 그림의 경계 박스의 정보를 담은 것이다. 2개의 경계 박스는 서로 모양이 비슷할수도 있지만, 네트워크가 학습하는 것이기 때문에 위에 설명한 조건하에 완전히 랜덤으로 나온다.

![yoloprocess3](https://user-images.githubusercontent.com/34755287/38285537-9bed94ec-37fb-11e8-9b83-5e318f269cfb.JPG)

- 위 그림은 한 grid cell의 예측 데이터 구조이다.

![yoloprocess4](https://user-images.githubusercontent.com/34755287/38285548-a96f8f26-37fb-11e8-8884-6c34bf8cef30.JPG)

- 한 grid cell의 첫번째 경계 박스의 확률(confidence)는 5칸 중 마지막에 위치해 있다. 이 값(스칼라, 숫자 1개)과 grid cell의 클래스 분류 확률과 곱하면 해당 경계 박스의 클래스 분류 확률을 구할 수 있다. 이것이 위 그림의 세로로 긴 노란색 박스이다. bb1(bound box 1)은 첫번째 후보의 경계 박스에 대한 클래스 점수표이며, 경계 박수의 오브젝트가 있을 확률이 매우 낮다면(0에 가까우면), 그에 해당하는 클래스 점수도 매우 낮아진다.

![yoloprocess5](https://user-images.githubusercontent.com/34755287/38285711-6ff39d2c-37fc-11e8-8a35-9e61f56ea5a9.JPG)

- 두번째 경계 박스의 클래스 점수표를 만드는 모습이다.

![yoloprocess6](https://user-images.githubusercontent.com/34755287/38285749-a37fd9da-37fc-11e8-8e5f-a642c1335feb.JPG)

- 첫번째 grid cell의 경계 박스 2개를 모두 처리한 모습이다.

![yoloprocess7](https://user-images.githubusercontent.com/34755287/38285780-d1c4146e-37fc-11e8-81d4-b8fcb28ccd12.JPG)

![yoloprocess8](https://user-images.githubusercontent.com/34755287/38285804-ea0ff038-37fc-11e8-981f-7aa8c94108cd.JPG)

- 마지막 grid cell까지 처리한 모습이며, 총 98개의 bound box가 추출된 모습이다.

![yoloprocess9](https://user-images.githubusercontent.com/34755287/38285862-1e328862-37fd-11e8-8fcb-f1b57def5152.JPG)

- 98개의 클래스 분류 점수표에서 값이 0.2보다 작은 것은 모두 0으로 채운다.(Thresholding)

![yoloprocess10](https://user-images.githubusercontent.com/34755287/38285904-6965c6b4-37fd-11e8-9aaf-bde99b07bd71.JPG)

- 위의 그림 과정까지 오면, 해당 경계 박스의 클래스가 무엇인지 알 수 있다.<br>
위 그림의 클래스 첫번째 자리는 Dog일 확률이고, 그 확률을 높은 값에서 낮은 값 순서대로 정렬한다. 만약 클래스 점수표중 하나를 제외하고 모두 0의 값이라면, 간단하게 해당 오브젝트를 알 수 있다. 하지만 0이 아닌 값이 여러개 있는 경우가 더욱 많다. 이것들을 제거하기 위해 NMS(Nom-maximal suppression, 비-최대값 억제) 알고리즘을 사용하여, 중복이 되는 경계 박스를 제거하고 오직 하나의 경계 박스만을 남긴다. 먼저 NMS 과정을 간단히 설명하면, 여러개의 경계 박스들이 겹쳐있을 때는 그중의 최대값 하나만을 남기고 모두 지운다. 그러나 경계 박스들이 겹쳐있지 않을 떄는, 각 경계 박스들이 서로 다른 오브젝트를 포함할 수 있다고 가정하고 그대로 그대로 나둔다.

![nms](https://user-images.githubusercontent.com/34755287/38286080-5fce624a-37fe-11e8-9cc9-a43b45d455fc.JPG)

- 위 그림은 NMS를 사용한 예로서, 겹치는 경계 박스들이 있을 떄의 상황이다.<br><br>

그렇다면, NMS에 대해 더 자세히 알아보자.

![nmsprocess1](https://user-images.githubusercontent.com/34755287/38286728-c84eee36-3801-11e8-9ef3-22f584169b5d.JPG)

- Dog에 대한 bbox confidence의 값이 내림차순으로 정렬하면 위의 그림과 같은 1 X 98 크기의 배열이 나온다.<br>
현재 0.2보다 낮은 값은 모두 지워줬는데, 위의 그림에서의 0.1값을 가진 경계 박스는 본 내용에서는 다루었는데, 0.1의 신뢰도가 어느정도의 그림인지 보여주려는 의도로 예상된다.

![nmsprocess2](https://user-images.githubusercontent.com/34755287/38286783-23138796-3802-11e8-9775-f112d4074265.JPG)

- 먼저, Dog에 대해 가장 높은 점수를 갖는 경계 박스는 bb47이고, 그 grid cell의 경계 박스를 나타낸 주황색 박스가 보인다.

![nmsprocess3](https://user-images.githubusercontent.com/34755287/38286839-70905cf6-3802-11e8-8ac3-fa3b60c0c62b.JPG)

- 2번째로 높은 점수를 갖는 경계 박스의 모습이다.<br>
첫번째의 주황색 박스보다 개의 엉덩이, 다리, 꼬리부분을 포착하지 못했기 때문이다.

![nmsprocess4](https://user-images.githubusercontent.com/34755287/38286881-ada2b77e-3802-11e8-91b4-f3066368d8cc.JPG)

- 개에 대해 3번째로 높은 점수를 받은 경계 박스이며, 개의 앞발만이 포착되었다.

![nmsprocess5](https://user-images.githubusercontent.com/34755287/38286910-d4ccba2a-3802-11e8-988f-5f4752d4206d.JPG)

- 그렇다면, 중복된 경계 박스를 지워보자. 가장 신뢰도 점수가 높은 첫번째와 두번째 박스의 모습이다. 두 경계 박스의 겹치는 부분이 50%가 넘는다. 이와 같은 경우 둘 중 하나는 중복된 오브젝트라고 판단하고, 둘 중 신뢰도가 높은 경계 박스를 선택한다. 그 결과 '0.3'의 bb20은 '0'의 값으로 바꿔준다.

![nmsprocess6](https://user-images.githubusercontent.com/34755287/38287095-d7a0f224-3803-11e8-9d10-5b1cbda96e9d.JPG)

- 그 다음 과정으로, 개에 대한 신뢰도 점수가 3번째로 높은 경계 박스(파란색)과 가장 높은 경계 박스(주황색)의 모습이다. 이 두 박스의 겹치는 부분은 50%가 넘지 않는다. 그러면, 파란색 박스안에는 주황색 박스안의 개가 아닌 또 다른 개가 있을 수도 있다고 판단하여, 두개의 값모두 지우지않고 넘어간다.<br><br>
위 그림에서는 2가지 경우만 다루었는데, 0이 아닌 값을 가진 모든 경계 박스를 서로 비교 및 처리한다. 그리고 나머지 19개의 클래스에 대해서도 모두 이와 같은 과정으로 처리한다. 그러면, 대부분의 값이 '0'이 된다. 하지만, 마지막 단계가 남아있다. 현재 입력 영상에는 개가 한 마리 뿐인데, NMS를 처리한 결과에서는 주황색 박스와 파란색 박스 두가지가 남아있다. 여기서 최종 박스를 결정하기위해 신뢰 점수가 '0.5'이하인 박스는 지운다. 그 결과, 파란색 박스가 지워지고 주황색 박스만이 최종적으로 남을 것이다.

![nmsprocess7](https://user-images.githubusercontent.com/34755287/38287444-a5329642-3805-11e8-9af6-10e550e6e3ec.JPG)

![nmsprocess9](https://user-images.githubusercontent.com/34755287/38287618-9dcc6454-3806-11e8-89f0-9fdd93834d21.JPG)

- NMS과정을 그림으로 정리하면 위와 같은 그림이다.<br>
한 경계 박스에 대해 20개의 클래스에 대한 신뢰 점수 중 가장 큰 값을 가지는 클래스가 최종 경계 박스이다. 만약 가장 큰 값이 0이라면, 해당 경계 박스에는 오브젝트가 없다는 뜻이다. 그러면, 경계 박스를 그리지 않는다. <br>
최종적으로 모든 연산이 끝나면 아래와 같은 사진이 나온다.

![nmsprocess8](https://user-images.githubusercontent.com/34755287/38287543-2d8612d0-3806-11e8-804f-0ecbe741f5c9.JPG)

### YOLO 학습

![yolor](https://user-images.githubusercontent.com/34755287/38463229-af4db958-3b30-11e8-91c8-5646265844bd.JPG)

위 그림의 로스 함수 **lambda_coord** = 5, **lambda_noobj** = 0.5 이고, S 는 격자의 수, B 는 각 grid cell의 경계박스 수이다.(S = 7, B = 2) Sum - squared error 를 사용한 이유는 최적화하기 편해서 일뿐, 평균 정밀도를 최대화하는데 적합한 함수는 아니다. 49개의 grid cell의 대부분은 오브젝트가 없기 때문에 신뢰 점수는 0에 가깝다. 이 문제를 해결하기 위해 오브젝트가 있는 경우에는 좌표 및 클래스 예측 로스는 키우고, 오브젝트가 없는 경우는 로스를 줄인다. 이 수식이 위의 **lambda_coord** = 5 (오브젝트가 있으면 5배 증가), **lambda_noobj** = 0.5 (오브젝트가 없다면 1/2 감소) 이다.<br><br>
**1^obj**, **1^noobj** 는 오브젝트가 있는 cell과 없는 cell을 구분한다. <br>
**{1_i}^obj** 는 오브젝트가 있는 grid cell i 이며, 경계박스와는 관계가 없다. <br>
**{1_i,j}^obj** 는 grid cell i 에 있는 j 번째 경계 박스가 예측한 값이다.<br>
이는 오브젝트가 있는 grid cell i 에서만 로스가 발생하며, 오브젝트가 없는 grid cell 은 로스 계산을 하지 않는다.<br>

위 그림의 식은 총 5 종류의 로스(loss)를 계산한다.<br>
1) 경계 박스의 x, y 좌표 학습하기
- Grid cell i 안에 오브젝트 i 가 있을 경우, 해당 grid cell 에서 예측한 경계 박스 j 가 정답과 같도록 학습한다.
- Grid cell 이 예측하는 2개의 경계 박스(j = 0, j = 1) 모두 정답과 같아지도록 유도한다.

2) 경계 박스의 width, height 학습하기
- 1. 과 동일한 식에서 루트를 씌운다. 그 이유는 큰 경계 박스에서는 width, height 를 조금만 키워도 넓이가 큰 폭으로 변한다.(미분값이 크다.), 하지만 작은 경계 박스에서는 width, height 를 많이 키워도 넓이의 변화가 작다. (미분값이 작다.) 이로서, sum - squared error를 사용하면 경계 박스가 큰 것과 작은 것의 미분 차이가 크게 나기 때문에 이를 반영하기 위해 루트를 사용한다.

3) Grid cell i 에 오브젝트가 있을 경우 클래스 예측하기
- 가장 평범함 로스이며, 이 오브젝트가 무엇인지 예측한다.

4) Grid cell i 에 오브젝트가 없을 경우 클래스 예측하기
- 3) 에 비해 가중치가 1/2 이기 때문에 3. 보다 덜 중요하다. 오브젝트가 없는 grid cell에서는 클래스 분류 정확도가 낮아도 상관없다. 이는 현재 grid cell이 배경일 확률이 높기 때문이다. 해당 grid cell의 가중치가 낮으므로 오브젝트가 있을 경우에서 더 정확히 클래스 분류를 할 수 있다.

5) 해당 줄에는 B 에 대한 Sum 연산이 없다. 그 이유는 각 grid cell i 에서 경계 박스는 2번(B = 2) 예측하지만, 클래스 확률 c 는 공유하기 때문이다.
<br>
위 식의 1., 2.은 좌표(coordinate) 부분을 학습한다. 이 부분을 간단히 구현하면 아래와 같다.

~~~
coordError = 0;
for ( i = 0; i < S * S; i++ ) {   // 7 * 7
    for ( j = 0; j < B; j++ ) {   // 2
        if(해당 cell에 오브젝트가 있다면) {
            coordError += ...   // 에러를 학습을 통해 줄여 나간다.
        else {
            // 해당 cell에는 오브젝트가 없으므로, 신경쓰지 않는다.
        }
    }
}
~~~

***

