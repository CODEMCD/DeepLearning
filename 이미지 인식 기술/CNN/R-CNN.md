# R-CNN
2014 : R-CNN - An Early Application of CNNs to Object Detection

![rcnn](https://user-images.githubusercontent.com/34755287/38193349-9ae2cb1c-36ac-11e8-861f-026ef2c26465.JPG)

R-CNN은 Image classification을 수행하는 CNN과 이미지에서 물체가 존재할 영역을 제안해주는 Region proposal 알고리즘을 연결하여 높은 성능의 Object detection을 수행할 수 있다. 수행과정은 다음과 같다.
1. 이미지를 입력한다.
2. 이미지로부터 약 2,000개의 Region proposal을 추출한다. (Selective search 알고리즘 활용)
3. 각 Region proposal 영역을 이미지로부터 잘라내고(Cropping) 동일한 크기로 만든 후(Warping), CNN(A modified version of AlexNet)을 활용하여 Feature을 추출하는데 마지막 단계에서 Support Vector Machine(SVM)을 사용하여 이미지를 분류한다.
4. 최종 분류된 물체의 바운딩 박스 좌표를 더 정확히 맞추기 위해 Linear regression 모델을 사용한다.

![rcnn2](https://user-images.githubusercontent.com/34755287/38194495-cae2a98a-36b2-11e8-866b-e8a6bf23ce28.JPG)

하지만 R-CNN은 몇 가지 문제점을 가지고 있다.
1. Test 속도가 느리다.
- 모든 Region proposal에 대해 전체 CNN path를 다시 계산한다.
- GPU(K40) 환경에서 한 장당 약 13초 정도 소요된다.
- CPU 환경에서 한 장당 약 53초 정도 소요된다.
2. SVM과 Bounding box regressor의 학습이 분리된다.
- Feature vector를 disk에 caching한다.
- CNN 학습 과정 후, SVM과 Bounding box regressor의 학습이 나중에 진행된다.(post-hoc)
3. 학습 과정이 복잡하다.
- 다단계 (Training pipeline)
- GPU(K40) 환경에서 약84시간이 소요된다.
