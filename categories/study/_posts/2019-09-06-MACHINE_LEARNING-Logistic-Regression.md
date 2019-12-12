---
layout: post
title: Machine Learning - Logistic Regression (평점 예측)
description: >
  Machine Learning - Logistic Regression (평점 예측)
excerpt_separator: <!--more-->
---

<!--more-->

# Machine Learning - Logistic Regression (평점 예측)

![영화리뷰](https://user-images.githubusercontent.com/27988544/64659261-b38af000-d475-11e9-9c28-9b3f3429b927.jpg)

```python
class Logistic_Regression_Classifier(object):
    # 인풋값의 sigmoid 함수 값을 리턴
    def sigmoid(self,z):
        return 1. / (1 + np.exp(-z))

    """
    X 데이터와 beta값들을 받아서 예측 확률P(class=1)을 계산.
    X 행렬의 크기와 beta의 행렬 크기를 맞추어 계산.
    """
    def prediction(self, beta_x, beta_c, X):
        # 예측 확률 P(class=1)을 계산하는 식을 만든다.
        return self.sigmoid(X.dot(beta_x) + beta_c)

    
    # beta값에 해당되는 gradient값을 계산하고 learning rate를 곱하여 출력.
    def gradient_beta(self, X, error, lr):
        # beta_x를 업데이트하는 규칙을 정의한다.
        beta_x_delta = X.T * error * lr / X.shape[0]
        # beta_c를 업데이트하는 규칙을 정의한다.
        beta_c_delta = np.mean(error) * lr
        
        return beta_x_delta, beta_c_delta

    """
    Logistic Regression 학습을 위한 함수.
    학습데이터를 받아서 최적의 sigmoid 함수으로 근사하는 가중치 값을 리턴.
    """
    def train(self, X, Y):
        # 학습률(learning rate)를 설정한다.(권장: 1e-3 ~ 1e-6)
        lr = 1
        # 반복 횟수(iteration)를 설정한다.(자연수)
        iters = 1000

        # beta_x, beta_c값을 업데이트 하기 위하여 beta_x_i, beta_c_i값을 초기화
        beta_x_i = np.zeros((X.shape[1], 10), dtype=float)
        beta_c_i = 0
    
        #행렬 계산을 위하여 Y데이터의 사이즈를 (len(Y),1)로 저장합니다.
        Y_hot = np.zeros((X.shape[0], 10), dtype=int)
        for i in range(len(Y)):
            Y_hot[i][Y[i] - 1] = 1
    
        for _ in range(iters):
            #실제 값 Y와 예측 값의 차이를 계산하여 error를 정의합니다.
            error = util.cross_entropy(util.softmax(self.prediction(beta_x_i, beta_c_i, X)), Y_hot)

            #gredient_beta함수를 통하여 델타값들을 업데이트 합니다.
            beta_x_delta, beta_c_delta = self.gradient_beta(X, error, lr)

            beta_x_i -= beta_x_delta
            beta_c_i -= beta_c_delta
            
        self.beta_x = beta_x_i
        self.beta_c = beta_c_i
        
    
    # 확률값을 0.5 기준으로 큰 값은 1, 작은 값은 0으로 리턴
    def classify(self, X_test):
        toSig = np.zeros((10), dtype=float)
        for i in X_test:
            for j in range(10):
                toSig[j] += self.beta_x[i][j]
        toSig = self.sigmoid(toSig + self.beta_c)
        return np.argmax(toSig) + 1

    
    # 테스트 데이터에 대해서 예측 label값을 출력해주는 함수
    def predict(self, X):
        nonzeros0 = X.nonzero()[0]
        nonzeros1 = X.nonzero()[1]
        predictions = []

        X_test = []
        for i in range(X.shape[0]):
            tmp = []
            X_test.append(tmp)

        for i in range(X.nnz):
            X_test[nonzeros0[i]].append(nonzeros1[i])

        X_test = np.array(X_test)
        
        for case in X_test:
            predictions.append(self.classify(case))

        return predictions


    """
    테스트를 데이터를 받아 예측된 데이터(predict 함수)와
    테스트 데이터의 label값을 비교하여 정확도를 계산
    """
    def score(self, X_test, Y_test):
        pred = self.predict(X_test)
        return util.getAcc(pred, Y_test)
```

