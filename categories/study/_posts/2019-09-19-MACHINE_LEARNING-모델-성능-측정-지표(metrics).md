---
layout: post
title: Machine Learning - 모델 성능 측정 지표(metrics)
description: >
  Machine Learning - 모델 성능 측정 지표(metrics)에 대하여
excerpt_separator: <!--more-->

---

<!--more-->

# Machine Learning - 모델 성능 측정 지표(metrics)

어떤 것을 제어하려면 관측할 수 있어야 한다. 성공의 지표가 모델의 최적화할 손실 함수를 선택하는 기준이 된다.

클래스 분포가 균일한 분류 문제에서는 정확도와  ROC AUC(Area Under the Curve)가 일반적인 지표이다. 클래스 분포가 균일하지 않은 문제에서는 정밀도와 재현율을 사용할 수 있다. 랭킹 문제나 다중 레이블 문제에는 평균 정밀도를 사용할 수 있다.

- <b>True, False, Positive, Negative</b>

  ![TFPN표](https://user-images.githubusercontent.com/27988544/65226607-21808880-db02-11e9-941d-ad81ae0bd3d2.png)

  - True Positive : 정답이 Positive인 것에 대해 예측값이 정답
  - True Negative : 정답이 Negative인 것에 대해 예측값이 정답
  - False Positive : 정답이 Positive인 것에 대해 예측값이 오답
  - False Negative : 정답이 Negative인 것에 대해 예측값이 오답

  <b>정확도(Accuracy)</b>는 전체 중에 맞은 것만 선택한다.

  <p align="center">$$Acc = \frac{TP + TN}{All}$$</p>
그러나 정확도는 클래스별 분포가 같을 때만 이용 가능하다.
  위의 예시에서 정상환자의 정확도는 998 / 990 = 99.8% 이지만,
  암환자의 정확도는 9 / 10 = 90% 이다.
  전체 정확도는 99.7%로 암환자의 정확도 (90%)가 정확하게 반영되지 않는다.
  

Accuracy의 단점을 보완하는 성능척도가 Precision과 Recall, 그리고 ROC curve와 AUC이다.

- <b>Precision과 Recall</b>

  <p align="center">$$P = \frac{TP}{TP + FP}$$, $$R = \frac{TP}{TP + FN}$$</p>
  즉, Precision은 Positive라고 판단한 것 중에서 실제로 정답이 True인 비율
  Recall은 실제 True인 것 중에서 Positive라고 예측한 비율
  
- <b>ROC(Receiver Operating Characteristic curve) curve와 AUC(Area Under the Curve)</b>

  ROC curve는 보통 binary classification이나 medical application에서 많이 쓰는 성능 척도.

  ![ROCcurve](https://user-images.githubusercontent.com/27988544/65228479-84bfea00-db05-11e9-87db-a98556cbd10f.png)

  첫번째 그래프에서 양 끝쪽은 판단이 쉽지만, 가운데 쪽은 판단이 불분명할 수 있다.
  이 상황에서 최선의 판단(초록색 선을 그는 행위)을 하려면 항상 error가 포함될 수 밖에 없다.
  ROC curve는 판단선(초록색 선)을 움직이면 어느 정도로 error가 걸러내질지 판단할 수 있는 그래프. 즉, 빨간석이 더 좋은 성능을 보인다고 판단할 수 있다.

  만약 분포가 겹치는 부분이 많을 수록 ROC curve는 직선에 가까워진다.

- <b>ROC curve의 이용</b>

  특징을 찾는데 활용 가능하다. 양질의 데이터라면, 그리고 더 좋은 성능의 모델일 수록 ROC curve는 더 넓은 면적을 가진다.

  즉 ROC curve가 좋은 데이터, 특징을 사용한다.
  머신러닝의 경우 raw data에서 내가 정하는 Decision Boundary에 덜 민감하면서 label을 구분하는데 더 믿음이 가고 잘 예측되겠다를 결정할 수 있다.
  딥러닝의 경우에는 클래스별 분포가 다를 때 accuracy의 대용으로 사용한다. 특히 딥러닝 모델 비교.

  사이킷런은 ROC AUC를 위한 roc_auc_score()함수와 평균 정밀도를 위한 average_precision_score()함수를 제공한다.