# Lecture 5 - 1 Logistic (regresstion) classification

## Binary Classification
- Spam Email Detection: Spam or Ham

- Facebook feed: show or hide
  - 타임라인중 어떤 것을 보여줄지 판단

- Credit Card Fraudulent Transaction detection: legitimate or fraud
  - 기존 사용자의 신용 카드 소비 패턴을 인식해서 그와 다르면 '가짜'라고 판단
  
## 0, 1 Encoding
- Spam Email Detection: Spam(1) or Ham(0)
- Facebook feed: show(1) or hide(0)
- Credit Card Fraudulent Transaction detection: legitimate(0) or fraud(1)

## Pass(1)/Fail(0) based on study hours => Linear Regression?
![5-1_1](https://user-images.githubusercontent.com/34755287/40427306-46ab96ce-5ed8-11e8-8c6e-c5e2f39faf82.PNG)

## Linear regression
- We know Y is 0 or 1
  H(x) = Wx + b
  
- **Hypothesis can give values large than 1 or less than 0**</br>
  - 위의 그래프와 같이 결과가 pass가 나와야 할 데이터가 너무 큰 값이나 너무 작은 값에 의해 그래프가 변하여 fail이 나올 수 있다.
  
## Logistic Hypothesis
- Hypothesis 값이 0 ~ 1 사이 값으로 정해져야 한다.
- **Sigmoid**</br>
  - Logistic function이나 sigmoid function이라고 불린다.
  
![5-1_2](https://user-images.githubusercontent.com/34755287/40427392-8bb6c8a6-5ed8-11e8-85ec-ca62c106484a.PNG)

![5-1_3](https://user-images.githubusercontent.com/34755287/40427595-2054cec2-5ed9-11e8-91a4-9587113e1bb4.PNG)

# 

## Cost function
![5-2_1](https://user-images.githubusercontent.com/34755287/40428072-702d4360-5eda-11e8-9b45-4c54142f217e.PNG)

### Linear regression
H(x) = Wx + b
- 아래로 볼록한 그래프기 때문에 하나의 최소지점이 보장된다.
 
### Sigmoid regression
![5-1_3](https://user-images.githubusercontent.com/34755287/40428120-9272b6c6-5eda-11e8-9ff1-2b127606aed9.PNG)

![5-2_2](https://user-images.githubusercontent.com/34755287/40428641-b4b4e80c-5edb-11e8-8412-acc5d3185255.PNG)

- 찾아야 할 대상은 global minimum인데 시작점에 따라 local minimum에서 최소라고 인식하고 종료한다.
- **결과적으로 시작점마다 최소지점이 다르게 나타나기 때문에 사용할 수 없다. => Cost 함수를 변경해야한다.**

## New cost function for logistic
![5-2_3](https://user-images.githubusercontent.com/34755287/40429423-a060ebc4-5edd-11e8-9789-2797bd92e25c.PNG)

- log를 사용한 이유는 구불구불한 선을 일직선으로 펼 수 있게 해주기 때문이다.
- 위의 cost함수를 사용할 수 있는지 예제를 통해 증명해본다.
1) y = 1: H(x) = 1 -> cost = 0, H(x) = 0 -> cost = 무한대 (위 그림의 왼쪽 그래프)

 2. y = 0: H(x) = 0 -> cost = 0, H(x) = 1 -> cost = 무한대 (위 그림의 오른쪽 그래프)
- 위 예제를 분석하면, 정답일 때 cost 값이 0, 틀리면 cost 값은 무한대로 결정된다.

## 코드 구현을 위한 cost function
![5-2_4](https://user-images.githubusercontent.com/34755287/40430085-3f25e272-5edf-11e8-96e9-57db68f5bb85.PNG)

- if문을 삭제하여 코드 구현하는데 간단하게 표현하기 위함이다.

## Minimize cost - Gradient decent algorithm
![5-2_5](https://user-images.githubusercontent.com/34755287/40430184-85853326-5edf-11e8-9d75-4d4aee165384.PNG)

- 마지막으로, 새로운 cost 함수를 최소화하기 위해 Gradient decent algorithm을 사용한 모습이다.
