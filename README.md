# 소프트웨어 캡스톤디자인 연구 

# 주제 :영상 열에서 손 검출과 수화 인식

### 산업경영공학과 16학번 김홍연

***과제 주요 내용
1) 자료수집 및 데이터 확보
2) 사용 알고리즘 비교 분석
3) 손 객체 탐지(by. YoloV4)
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
 ![image](https://user-images.githubusercontent.com/62275592/116673922-ee40f380-a9de-11eb-9ee6-b4992bf116bd.png)
![image](https://user-images.githubusercontent.com/62275592/116675748-20ebeb80-a9e1-11eb-8fc6-affb07bd1662.png)
- 600개 범주의 15,851,536개 레이블 박스
- 350개 범주의 2,785,498개 인스턴스 분할
- 1,466개의 관계에 대한 3,284,280개의 관계 주석
- 675,155개의 지역화된 서술
- 19,957개 범주의 59,919,574개 이미지 수준 레이블
- 6,000개 이상의 범주가 있는 크라우드소싱 이미지 478,000개

-<b>OID toolkit을 활용해 이미지와 레이블 데이터 함께 다운로드</b>
![image](https://user-images.githubusercontent.com/62275592/116675851-3d882380-a9e1-11eb-901c-b228545c9067.png)
![image](https://user-images.githubusercontent.com/62275592/116679449-7cb87380-a9e5-11eb-8420-052d29e1edff.png)



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


#### 정확도간의 큰 차이는 없지만 비교적 탐색 시간이 빠른 Yolo 알고리즘 선택


## 3. 손 객체 탐지(by. YoloV4)

### 1) Darknet 다운로드
<b>기존 깃허브에 존재하는 AB의 repository로부터 makefile을 조정하여 Darknet용 OPENCV 및 GPU를 사용하도록 설정한 다음 Darknet을 빌드</b>
![image](https://user-images.githubusercontent.com/62275592/116676391-e33b9280-a9e1-11eb-9f38-cab07dd9dedc.png)

### 2) 전이학습을 위한 background 가중치 다운로드
<b>기존에 코코 데이터셋 기반 80개의 분류 학습되어 있던 가중치 다운로드</b>
![image](https://user-images.githubusercontent.com/62275592/116676554-11b96d80-a9e2-11eb-89c5-937ffd44299d.png)

### 3) 불러온 가중치로 일반적인 객체 탐지 테스트
<b>학습되어 있는 객체는 높은 확률로 탐지</b>
![image](https://user-images.githubusercontent.com/62275592/116678065-de77de00-a9e3-11eb-8c36-16d3d1b7a974.png)

### 4) 하이퍼 파라미터 튜닝
-batch=64 / subdivisions=16
<br>-배치 사이즈는 64로 배치 학습을 통하여 많지 않은 데이터로도 안정적인 학습을 수행할 수 있도록 하였고,yoloV4 알고리즘에서 자주 쓰이는 부분 분할을 16으로 설정
<br>-max_batches = 6000
<br>-최대 배치 사이즈는 보통 클래스의 2000배가 적당하지만 one class classification이므로 6000장 설정
<br>-width=416 / height=416
<br>-416은 32의 제곱으로 수월한 학습속도를 보장하기 위해 너무 크게 설정하진 않음
<br>-steps=4800,5400
<br>-스텝은 최대 배치의 80%와 90% 선에서 적절한 학습 성능을 보장하기에 각 4800, 6000번으로 설정
<br>-random = 1
<br>-랜덤 항목은 0과 1의 값을 설정할 수 있는데 이는 층 간 사이즈의 변형을 허용하는지에 관한 여부. 정확도의 향상을 위해 층 간의 어느 정도 사이즈의 변형을 허용
<br>-이것 외에도 필터는 (클래스 수 + 1)*3을 보장하고 컨볼루션 층의 패딩과 스트라이드를 본 학습에 맞게 설정하였으며 배치 정규화는 커널 하나 당 수행하도록 하는 등의 추가적인 파라미터 튜닝을 진행.


![image](https://user-images.githubusercontent.com/62275592/116679927-0c5e2200-a9e6-11eb-905d-a1e2418dc767.png)
batch=64 / subdivisions=16 

### 5) 결과 확인
<h4>손에 대한 객체 탐지 이미지 내에서 바운딩 박스 표현</h4> 

![image](https://user-images.githubusercontent.com/62275592/120881657-a597de00-c60d-11eb-840f-d2021cd3220f.png)
<br>
<h4>손 객체에 대한 레이블 정보를 txt파일 형태로 저장</h4> 

![image](https://user-images.githubusercontent.com/62275592/120881810-b9900f80-c60e-11eb-873c-cd303a8a365a.png)

## 4. 수화 이미지 분류(by. 4-layer CNN[지도학습] vs ResNet[전이학습])

### 1) 4-layer CNN 지도학습
<h4>mnist 상에 존재하는 수화 데이터 셋을 사용하여 4-layer classifier를 생성 및 학습</h4>
 사용 데이터셋: mnist 수화 데이터셋

- 출처:https://www.kaggle.com/datamunge/sign-language-mnist 
![image](https://user-images.githubusercontent.com/62275592/120883231-f2cc7d80-c616-11eb-984f-8b5ce75caf14.png)

- 데이터 특성: csv형태로 저장되어 있으며, 28 x 28nb pixels, 총 1500 여개의 데이터
![image](https://user-images.githubusercontent.com/62275592/120883216-ddefea00-c616-11eb-97c5-14478cdc0eeb.png)

<h4>모델 구성</h4>
은닉층 활성화 함수는 오류역전파의 가중치 소실을 최소화 하기 위한 RELU 함수로 설정
<br>출력층 활성화 함수는 이산적인 분류를 위한 softmax 함수로 설정
<br>과도한 학습을 막기 위해 각 층마다 dropout 비율 0.2로 설정
<br>optimizer는 모멘텀의 적응적 학습을 통해 대부분의 최적화 방식보다 성능이 좋은 ADAM으로 설정 

![image](https://user-images.githubusercontent.com/62275592/120883563-d0d3fa80-c618-11eb-810b-80d7fd1dfad2.png)

<h4>결과</h4>

![image](https://user-images.githubusercontent.com/62275592/120883777-19d87e80-c61a-11eb-883a-ebac71d50b99.png)

