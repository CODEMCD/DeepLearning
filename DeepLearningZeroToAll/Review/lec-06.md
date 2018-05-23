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
