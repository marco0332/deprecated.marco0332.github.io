# Machine Learning - Naive Bayes

![naivebayes](https://user-images.githubusercontent.com/27988544/64659133-1d56ca00-d475-11e9-8e22-a5008417c98e.jpg)

```python
class Naive_Bayes_Classifier(object):
    """
    feature 데이터를 받아 label(class)값에 해당되는 likelihood 값들을
    naive한 방식으로 구하고 그 값의 log값을 리턴
    """
    def log_likelihoods_naivebayes(self, feature_vector):
        log_likelihood = np.zeros(feature_vector.shape[0])

        smoothing = 1
        smooth_V = feature_vector.shape[0]
        num_feature = np.sum(feature_vector) + smooth_V

        for i in range(smooth_V):
            log_likelihood[i] = np.log(feature_vector[i] + smoothing) - np.log(num_feature)

        return log_likelihood

    """
    feature 데이터를 받아 label(class)값에 해당되는 posterior 값들을
    구하고 그 값의 log값을 리턴
    """
    def class_posteriors(self, feature_vector):
        log_likelihood = np.zeros((self.nclass), dtype=float)

        for i in range(1, log_likelihood.shape[0]):
            for feature_index in range(len(feature_vector)):
                log_likelihood[i] += self.likelihoods[i][feature_vector[feature_index]]

        return log_likelihood + self.log_prior

    """
    feature 데이터에 해당되는 posterir값들(class 개수)을 불러와 비교하여
    더 높은 확률을 갖는 class를 리턴
    """
    def classify(self, feature_vector):
        log_posterior = self.class_posteriors(feature_vector)

        return np.argmax(log_posterior[1:]) + 1

    """
    트레이닝 데이터를 받아 학습하는 함수
    학습 후, 각 class에 해당하는 prior값과 likelihood값을 업데이트
    """
    def train(self, X, Y):
        self.nclass = 11
        nz0 = X.nonzero()[0]
        nz1 = X.nonzero()[1]

        # label(1 ~ 10)에 해당되는 각 feature 성분의 개수값(num_token) 초기화 
        num_token = np.zeros((self.nclass, X.shape[1]), dtype=int)

        for i in range(X.nnz):
            num_token[Y[nz0[i]]][nz1[i]]+=1

        # smoothing을 사용하여 각 클래스에 해당되는 likelihood값 계산
        self.likelihoods = np.zeros((self.nclass, X.shape[1]), dtype=float)
        for i in range(1, self.nclass):
            self.likelihoods[i] = self.log_likelihoods_naivebayes(num_token[i])

        # 각 class의 prior를 계산
        # pior의 log값 계산
        self.log_prior = np.zeros((self.nclass), dtype=float)

        for i in range(1, self.nclass):
            self.log_prior[i] = np.log(num_token[i].sum()) - np.log(X.nnz)

    """
    테스트 데이터에 대해서 예측 label값을 출력해주는 함수
    """
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
    테스트 데이터를 받아 예측된 데이터(predict 함수)와
    테스트 데이터의 label값을 비교하여 정확도를 계산
    """
    def score(self, X_test, Y_test):
        pred = self.predict(X_test)
        return util.getAcc(pred, Y_test)
```

