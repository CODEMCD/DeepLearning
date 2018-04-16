# Faster R-CNN

![fasterrcnn](https://user-images.githubusercontent.com/34755287/38198494-06b6bfe0-36c8-11e8-9bb0-a8c76bd593ff.JPG)

Faster R-CNN은 기존 Fast R-CNN 속도의 bottleneck이었던 region proposal생성을 CNN 외부(CPU환경)의 알고리즘이 아닌, CNN 내부(GPU 환경)에 region proposal을 생성할 수 있는 network, **Region Proposal Network(RPN)**를 설계함으로써 더욱 빠르고 정확한 region proposal을 생성할 수 있다.

## Region Proposal Network(RPN)

![rpn](https://user-images.githubusercontent.com/34755287/38197083-75d2a904-36c1-11e8-9598-52c035bff85b.JPG)

RPN은 feature map 정보를 활용해 물체가 존재하는 위치를 출력으로 가지는 네트워크이다. 먼저 feature map 위의 N x N 크기의 작은 window 영역을 입력 받고, 해당 영역에 물체가 존재하는지/존재하지 않는지에 대한 binary classification을 수행하는 작은 classification network와 위치 보정을 위한 bounding box regression을 사용한다. 하나의 feature map에서 모든 영역에 물체 존재 여부를 확인하기 위해 sliding window 방식으로 탐색한다.

![archorrpn](https://user-images.githubusercontent.com/34755287/38198084-44d3e016-36c6-11e8-8324-029924368538.JPG)

하지만, 위의 방식은 이미지에 존재하는 물체의 다양한 크기와 비율에 사용하기에는 부족하다. 이러한 단점을 보완하기 위해 미리 정의된 여러 크기와 비율의 reference box k를 정해놓고 각각의 sliding window 위치마다 k개의 box를 출력하도록 설계한다. 이러한 방식을 anchor라고 한다. 즉 RPN의 출력값은, 모든 anchor 위치에 대해 각각 물체/배경을 판단하는 2k개의 classification 출력과 x, y, w, h 위치 보정값을 위한 4k개의 regression 출력을 가지게 된다. Feature map의 크기가 W x H라면, 하나의 feature map에 총 W * H * K개의 anchor가 존재한다. 해당 논문에서는 3가지 크기(128, 256, 512)와 3가지 비율(2:1, 1:1, 1:2)을 사용해 총 anchor k = 9를 최종적으로 사용하였다.

밑의 표는 Faster R-CNN의 실험 결과이다. PASCAL VOC 2007 test set을 사용한 실험에서, Faster R-CNN은 R-CNN의 250배, Fast R-CNN의 10배 빠른 속도를 보여주었다.

![rcnnres](https://user-images.githubusercontent.com/34755287/38198295-279cfb6c-36c7-11e8-91d2-36af5a90b128.JPG)

Faster R-CNN의 특징을 정리하면 다음과 같다.
- RPN + Fast R-CNN
- Region proposal을 network 내부에서 계산한다.
- RPN은 fully convolutional 하다.
- RPN은 end-to-end로 학습된다.
- RPN은 detection network와 convolutional feature map을 공유한다.
