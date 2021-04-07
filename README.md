# 데이터분석 캡스톤디자인 연구 

# 주제 :데이터 분석을 통한 국내 전략적 관광지 선정

### 산업경영공학과 16학번 김홍연

***과제 주요 내용
1) 여행지 데이터 탐색(EDA)
2) 데이터 가공
3) Random Forest 모델 적용
4) XGBoost 모델 적용
5) Light GBM 모델 적용
6) DNN 모델 적용

## 1. 여행지 데이터 탐색

### 1) 변수 데이터 셋

![image](https://user-images.githubusercontent.com/62275592/102598961-25191a00-4160-11eb-8de9-1d95a03d5bfa.png)



- 관광지식정보시스템에서 제공하는 국내 48000명의 여행자 통계 기반 관광 데이터 확보

### 2) 여행지 별 만족도 평가
![image](https://user-images.githubusercontent.com/62275592/102602630-23058a00-4165-11eb-8d20-e3ce02ea7fd8.png)

- 만족도 상위 3개의 지역: 제주도, 강원도, 전라남도(12.2, 11.8, 11.3)
- 만족도 하위 3개의 지역: 광주, 대전, 서울(7.9, 8.4, 8.5)

## 2. 데이터 가공

### 1) 명목형 데이터 one-hot Encoding
![image](https://user-images.githubusercontent.com/62275592/102603449-2b11f980-4166-11eb-8f75-cbfb81a9aaca.png)

- pandas의 get-dummies 함수를 통하여 30개의 명목형 변수들을 227개의 이진 변수로 변환

### 2) Max-Normalization

![image](https://user-images.githubusercontent.com/62275592/102603642-71675880-4166-11eb-8657-553d23d9d270.png)

- 수치형 데이터에 대한 max-normalization


### 3) 변수 범주화
![image](https://user-images.githubusercontent.com/62275592/102604168-23068980-4167-11eb-8ca0-2cf81468047b.png)

-일자별 데이터를 계절별 데이터로 범주화


### 4) 타겟 변수 설정
![image](https://user-images.githubusercontent.com/62275592/102604268-4598a280-4167-11eb-85fb-3241d39e0d9d.png)

-여행객의 선호도를 평가하기 위해 만족도, 재방문정도, 추천정도의 세가지 변수를 합하여 타겟 변수로 설정
- 최종 값이 12 이상이면 만족, 11 이하면 불만족의 이진(binary) 변수로 설정

## 3. Random Forest 모델 적용
주어진 데이터 셋에서 복원 랜덤 샘플링을 통하여 여러 학습데이터 셋을 만들고, 만들어진 학습 데이터를 모델에 적용시키고 나서 
예측된 여러 결과값들을 집계하여 최종 결과값을 산출하는 앙상블 bagging  기법의 tree 모델
### 모델 작성
![image](https://user-images.githubusercontent.com/62275592/102604703-edae6b80-4167-11eb-9d0b-61f300cf1dd0.png)

- train/test 비율: 6:4, random_state: 20, 약 생성자 개수 :5개 
- test 평가 accuracy기준 train accuracy: 97%, test accuracy:87%

![image](https://user-images.githubusercontent.com/62275592/102605288-68778680-4168-11eb-9221-ddb2745654d2.png)
- 실제 불만족한 사람들에 대한 만족의 예측: 455명

![image](https://user-images.githubusercontent.com/62275592/102605502-b4c2c680-4168-11eb-8989-3a2b0f82191a.png)

-  상위 노드로 뽑힌 주요 노드: 총 경비, 교통비, 1인당 식비 

## 4. XGBoost 모델 적용
XGBoost는 트리 기반 앙상블 모델에서 bagging이 아닌 boosting을 활용한 예측 모델로 
boosting이란 가중치를 부여하고 각 모델들을 연속적으로 학습시켜 약 분류기를 강 분류기로 만드는 기법
### 0) 하이퍼 파라미터 설정

![image](https://user-images.githubusercontent.com/62275592/102605831-3adf0d00-4169-11eb-8e76-0edaf79a9f0e.png)

- 학습수행 객체함수: 이진분류
- 리프노드 추가분할 최소손실값(감마): 0.25
- boost 모델 이전 약생성기에서의 학습률: 10%
- 트리 최대 깊이: 6
- L2 Regularization 적용 값: 10
- 불균형 데이터셋의 균형 유지정도: 3

![image](https://user-images.githubusercontent.com/62275592/102606748-94940700-416a-11eb-9f7d-96b43512e0f9.png)
- test accuracy: 87.6%
- 실제 불만족한 사람들에 대한 만족의 예측: 291명

![image](https://user-images.githubusercontent.com/62275592/102607069-0704e700-416b-11eb-8477-21f253eec9be.png)
- 상위 노드로 뽑힌 주요 노드: 교통비, 여행 이유, 숙박비 등 

## 5. Light GBM 모델 적용
XGBoost와 마찬가지로 boosting을 통한 약 분류기 사이의 연속적 학습을 통해 강 분류기를 만드는 모델. 
lightGBM의 특징은 일반적인 균형 트리 분할(Level Wise)방식과 다르게 리프 중심 분할(Leaf Wise)방식을 사용하기 때문에,
최대 손실 값을 갖는 리프 노드를 지속적으로 분할하면서 트리를 보다 깊고 비대칭적으로 생성.
![image](https://user-images.githubusercontent.com/62275592/102607705-10428380-416c-11eb-8d33-746c903c3dfd.png)
- train/test 비율: 6:4, boosting type: Gradient boosting

![image](https://user-images.githubusercontent.com/62275592/102608101-b2fb0200-416c-11eb-8b7e-6f896fd562b5.png)
-test 평가 accuracy기준 train accuracy: 94.4%, test accuracy:87.5%

![image](https://user-images.githubusercontent.com/62275592/102608268-f8b7ca80-416c-11eb-9174-33159c00a684.png)
- 상위 노드로 뽑힌 주요 노드: 여행 인원, 교통비 등

## 6. Deep Neural Network(DNN) 모델 적용
 다중의 은닉층을 포함하여 다양한 비선형적 관계를 학습하는 black box 모델의 심층 학습 기법
 ### 0) 하이퍼 파라미터 설정
 ![image](https://user-images.githubusercontent.com/62275592/102608603-75e33f80-416d-11eb-97a1-5a2998afbf06.png)
- 은닉층 활성화함수: ReLU
- 출력층 활성화함수: Sigmoid
- 1계층과 2계층 사이의 25% Drop out
- 총 6559개의 파라미터를 활용한 학습
- L2 Regularization 적용 값: 10
- 불균형 데이터셋의 균형 유지정도: 3

 ### 1) grid search를 통한 최대의 accuracy 선정
 ![image](https://user-images.githubusercontent.com/62275592/102609150-58fb3c00-416e-11eb-89cb-2183279fb235.png)
![image](https://user-images.githubusercontent.com/62275592/102609201-703a2980-416e-11eb-87df-1ff0b032cd80.png)
- batch size: 10, epoch: 5에서 최대 test accuracy 86%

![image](https://user-images.githubusercontent.com/62275592/102609513-f8203380-416e-11eb-9661-e242a8c7f53f.png)

