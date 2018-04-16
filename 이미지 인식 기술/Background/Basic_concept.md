# 2 - Stage Detection
2-stage detection은 R-CNN계열이 여기에 해당된다. R-CNN계열에는 R-CNN, Fast R-CNN, Faster R-CNN, SPP-Net, R-FCN, Mast R-CNN등이 있다. 이 구조의 공통적인 단점은 
1. 복잡한 pipeline
2. 느리다. (real time 환경에서 실행 불가능)
3. 각 component를 optimize하기 어렵다.

이러한 점을 극복하기 위해, 최근에는 Unified Detection에 해당하는 연구들이 진행되고 있다. 대표적으로 Yolo, SSD가 이에 해당된다.

# Bounding Box Regression
Bounding box의 parameter를 찾는 regression을 의미한다. 초기의 region proposal과 CNN이 예측한 결과가 서로 맞지 않을 있다. 그렇기 때문에 Bounding box regressor는 CNN의 마지막 pooling layer에서 얻은 feature 정보를 사용하여 region proposal의 regression을 계산한다.
