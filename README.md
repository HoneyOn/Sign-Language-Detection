# 소프트웨어 캡스톤디자인 연구 

# 주제 :영상 열에서 손 검출과 수화 인식

### 산업경영공학과 16학번 김홍연

***과제 주요 내용
1) 자료수집 및 데이터 확보
2) 사용 알고리즘 비교 분석

## 1. 자료수집 및 데이터 확보

### 1) ASL(American Sign Language) 데이터셋
- 출처:https://www.kaggle.com/grassknoted/asl-alphabet
- 데이터 특성: A ~ Z 까지의26개의 class, 각 Train DataSet 3000장

![image](https://user-images.githubusercontent.com/62275592/113810732-cecef600-97a5-11eb-9672-e84f8fc6602b.png)


### 2) 데이터 라벨링
- 2차원 데이터 라벨링을 위한 바운딩 박스를 데이터 전체 비율로 설정<p>(손에 대한 이미지만을 가져왔기 때문에 전체 비율)</p>
  
- 순서대로 [ class / x_center / y_center / width / height ]의 정보를 의미

![image](https://user-images.githubusercontent.com/62275592/113811838-e9a26a00-97a7-11eb-8c7b-c82938ee98d0.png)


### 3) 사전학습 데이터셋 확보
- MS coco dataset 사용 <p>https://cocodataset.org/#home</p>
 ![image](https://user-images.githubusercontent.com/62275592/113836078-888a8e80-97c7-11eb-9d3a-45e6902103ce.png)
-	객체 탐지 모델에서 사용빈도가 월등히 높은 데이터셋
-	33만개 이상의 이미지(>20만 labeled)
-	150만개 이상의 객체
-	80개 이상의 클래스

-Google open images DataSet V6 사용 <p>https://storage.googleapis.com/openimages/web/index.html</p>
- ![image](https://user-images.githubusercontent.com/62275592/116673922-ee40f380-a9de-11eb-9ee6-b4992bf116bd.png)
- 600개 범주의 15,851,536개 레이블 박스
- 350개 범주의 2,785,498개 인스턴스 분할
- 1,466개의 관계에 대한 3,284,280개의 관계 주석
- 675,155개의 지역화된 서술
- 19,957개 범주의 59,919,574개 이미지 수준 레이블

확장 - 6,000개 이상의 범주가 있는 크라우드소싱 이미지 478,000개
## 2. 사용 알고리즘 비교 분석
- <b>대상: R-CNN, R-FCN, YOLO, SSD</b>
- 
![image](https://user-images.githubusercontent.com/62275592/113839427-a3aacd80-97ca-11eb-86ef-c7fcbaaecb74.png)

-<b>영상 열에서 수화 인식시 필요한 기능</b> <p> 1)영상 데이터에 대한 빠른 객체 탐지</p><p>  2)빠른 학습시간</p><p>  3)edge tech의 실활용을 위한 가벼운 알고리즘</p>

-<b>알고리즘 간 정확도 비교</b>

![image](https://user-images.githubusercontent.com/62275592/113838420-b7096900-97c9-11eb-80f7-ac3a2126c3ca.png)

-<b>알고리즘 간 객체 탐지 시간 비교</b>

![image](https://user-images.githubusercontent.com/62275592/113838596-e1f3bd00-97c9-11eb-9ca9-952d350f47b1.png)

-출처: https://iopscience.iop.org/article/10.1088/1757-899X/844/1/012024/pdf


### 정확도간의 큰 차이는 없지만 비교적 탐색 시간이 빠른 Yolo 알고리즘 선택



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

