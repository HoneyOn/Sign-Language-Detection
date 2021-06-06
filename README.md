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

## 4. 수화 이미지 분류(4-layer CNN[지도학습] vs ResNet[전이학습])

### 1) 4-layer CNN 지도학습
<h4>1-1)mnist 상에 존재하는 수화 데이터 셋을 사용하여 4-layer classifier를 생성 및 학습</h4>
 사용 데이터셋: mnist 수화 데이터셋

- 출처:https://www.kaggle.com/datamunge/sign-language-mnist 
![image](https://user-images.githubusercontent.com/62275592/120883231-f2cc7d80-c616-11eb-984f-8b5ce75caf14.png)

- 데이터 특성: csv형태로 저장되어 있으며, 28 x 28nb pixels, 총 1500 여개의 데이터
![image](https://user-images.githubusercontent.com/62275592/120883216-ddefea00-c616-11eb-97c5-14478cdc0eeb.png)

<h4>1-2)모델 구성</h4>
은닉층 활성화 함수는 오류역전파의 가중치 소실을 최소화 하기 위한 RELU 함수로 설정
<br>출력층 활성화 함수는 이산적인 분류를 위한 softmax 함수로 설정
<br>과도한 학습을 막기 위해 각 층마다 dropout 비율 0.2로 설정
<br>optimizer는 모멘텀의 적응적 학습을 통해 대부분의 최적화 방식보다 성능이 좋은 ADAM으로 설정 

![image](https://user-images.githubusercontent.com/62275592/120883563-d0d3fa80-c618-11eb-810b-80d7fd1dfad2.png)

<h4>1-3)결과</h4>

![image](https://user-images.githubusercontent.com/62275592/120883777-19d87e80-c61a-11eb-883a-ebac71d50b99.png)


<h4>1-4)원인 해석</h4>

- 1)28 x 28의 픽셀은 사진을 너무 뭉개뜨려 학습에 잡음이 많이 생긴다.

![image](https://user-images.githubusercontent.com/62275592/120883890-cadf1900-c61a-11eb-8eb2-d642c3c688ce.png)

- 2)1500장의 데이터셋으로 학습시키기에는 분류할 class에 비해 데이터의 양이 부족하다.

- 3)밑바닥부터의 4-layer 학습이라 분류기가 데이터 출처가 같는 자체적인 배경 따위의 잡음을 같이 학습한다.


### 2) ResNet 전이학습
<h4>2-1)imagenet에서 pretrained 된 ResNet 네트워크에 ASL 데이터 셋을 사용하여 수화 분류에 대한 전이학습</h4>
 사용 데이터셋: 1의 자료수집에서 확보한 ASL(American Sign Language) 데이터셋
 <br> (-경로설정, 이미지 사이즈: 224x224 pixel, 배치 사이즈: 32)
 
 ![image](https://user-images.githubusercontent.com/62275592/120885007-79865800-c621-11eb-8e78-a607d6d66372.png)

<h4>2-2)데이터 증강(data augumentation)</h4>
횡이동 비율: 0.2, 축이동 비율: 0.2, 회전 및 확대 범위: 0.2, 좌우 반전 허용, valid split: 40%
 
![image](https://user-images.githubusercontent.com/62275592/120885108-219c2100-c622-11eb-8e75-c70fe8493a3a.png)

<h4>2-3)모델 구현</h4>

![image](https://user-images.githubusercontent.com/62275592/120885260-f36b1100-c622-11eb-885a-cd749c7f0352.png)


- imagenet에서 학습시킨 네트워크를 전이학습 base model로 설정


- base model의 parameter는 데이터가 많이 확보된 것은 아니기에 freeze (trained data: 3600장)


- 은닉층 RELU, 출력층 Softmax, optimizer ADAM



<h4>2-4)결과</h4>

![image](https://user-images.githubusercontent.com/62275592/120911401-42b44e80-c6c2-11eb-97ca-9d05f0ceca2c.png)

test Accuracy :91%

<br> 꽤나 우수한 정확도지만 내가 직접 찍은 데이터에 대하여 비슷한 손모양의 알파벳들은 분류가 조금 안됨.
<br> 직접 만든 test data에 대한 분류

![image](https://user-images.githubusercontent.com/62275592/120911504-f584ac80-c6c2-11eb-802e-78fd5adfdb53.png)

![image](https://user-images.githubusercontent.com/62275592/120911477-bb1b0f80-c6c2-11eb-91bc-32fb02c9d4bd.png)


<h4>2-5) 정확도를 높이기 위한 추가적인 학습</h4>

- 분류가 어려운 A, E, S 등에 대하여 3000장씩 추가하여 학습

![image](https://user-images.githubusercontent.com/62275592/120911664-fcf88580-c6c3-11eb-8de3-96de8a768e8b.png)

![image](https://user-images.githubusercontent.com/62275592/120911671-14d00980-c6c4-11eb-9d28-4b23bfcb24e1.png)

![image](https://user-images.githubusercontent.com/62275592/120911709-55c81e00-c6c4-11eb-97fb-c75032c29ec3.png)

![image](https://user-images.githubusercontent.com/62275592/120911715-62e50d00-c6c4-11eb-8773-141dce7c28f5.png)

![image](https://user-images.githubusercontent.com/62275592/120911718-6d9fa200-c6c4-11eb-8d66-d0026f1a7343.png)

각 class의 test data마다 예측 확률에 대한 특성들이 존재하여,
최종 출력 함수에 특성을 고려한 threshold를 대입. (ex. s로 예측하기 위해선 S에 대하여 95% 이상의 정확도가 나와야한다)

![image](https://user-images.githubusercontent.com/62275592/120911760-dc7cfb00-c6c4-11eb-9dde-bf41b3eb05ee.png)

![image](https://user-images.githubusercontent.com/62275592/120911837-4bf2ea80-c6c5-11eb-94a2-ac0da503395c.png)

![image](https://user-images.githubusercontent.com/62275592/120911867-92e0e000-c6c5-11eb-8daa-36e9907cacca.png)

![image](https://user-images.githubusercontent.com/62275592/120911889-d0456d80-c6c5-11eb-9817-2893267bff04.png)


