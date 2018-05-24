# Lecture 6 - 1 Softmax classification: Multinomial classification

## Multinomial classification

![6-1_1](https://user-images.githubusercontent.com/34755287/40431465-7524dc72-5ee2-11e8-8f9c-2200376818f8.PNG)

- 2개를 가지고 구분하는 것이 아닌 3개 이상의 클래스를 구분이 가능해야 한다.

![6-1_2](https://user-images.githubusercontent.com/34755287/40431885-51f1546e-5ee3-11e8-8c2b-78d011b14aeb.PNG)

- 위의 3가지 클래스는 3개의 Linear classification으로 구분할 수 있다.

- 위의 그래프는 아래의 3가지 classification 구조로 나타낼 수 있고, 3개의 독립적인 벡터를 계산할 수 있다.

![6-1_3](https://user-images.githubusercontent.com/34755287/40432637-204abc8c-5ee5-11e8-8308-472d91141bf1.PNG)

- 위의 독립적인 3개의 벡터 계산을 밑의 그림처럼 하나의 벡터 계산으로 간단하게 구현할 수 있다.

![6-1_4](https://user-images.githubusercontent.com/34755287/40433371-d6b96440-5ee6-11e8-9d28-b5b5a255b67a.PNG)

# Lecture 6 - 2 Softmax classification: softmax and cost function

## Where is sigmoid?

![6-2_1](https://user-images.githubusercontent.com/34755287/40455765-52e5ca62-5f29-11e8-86a9-51d6cb381518.PNG)

- Simoid는 결과값을 0 ~ 1사이 값으로 설정한다. 그리고 softmax는 0 ~ 1사이의 p의 값 중 가장 큰 값을 정답이라고 도출하며, 이 p의 값의
합은 1이 된다.

## Softmax

![6-2_2](https://user-images.githubusercontent.com/34755287/40456734-df328100-5f2d-11e8-9542-bad4d485bcb5.PNG)
![6-2_3](https://user-images.githubusercontent.com/34755287/40456735-df5a55ea-5f2d-11e8-9fa7-7f42758aa70c.PNG)

- Softmax에서 p는 확률로 볼 수 있다. 확률 중 가장 큰 값을 도출해주는 방법을 **One - hot encoding**이락고 한다.

## Cost function

![6-2_4](https://user-images.githubusercontent.com/34755287/40457205-391fa768-5f30-11e8-8799-9c6e479f9201.PNG)

- S(y)는 정답의 추측 값이고, L은 실제 정답이다.
- 위의 식을 Cross-entropy 라고 부른다.
## Cross-entropy cost function

![6-2_5](https://user-images.githubusercontent.com/34755287/40457566-3c6f5eb6-5f32-11e8-8bac-766997c052c0.PNG)

- 위의 예제에서 볼 수 있듯이, 정답을 도출하면 cost 값이 0이고, 틀린 답을 도출하면 cost 값이 무한대 값이 나오는 것을 알 수 있다.

## Logistic cost VS cross entropy

- Logistic cost 와 cross entropy는 서로 같다. (증명해볼것 숙제)

## Gradient descent

![6-2_6](https://user-images.githubusercontent.com/34755287/40457794-661aabfc-5f33-11e8-8227-e254ccc9c6b6.PNG)

- cost 함수 식은 위의 그래프처럼 아래로 볼록한 형태이므로 gradient descent 알고리즘을 사용하면 최소값을 찾을 수 있다.
