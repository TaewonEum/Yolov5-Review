# Yolov5 Review

One Stage 모델로 클래스 예측과 바운딩 박스 예측을 한번에 수행함

**크게 Backbone, Head 부분으로 구성됨**

## Yolov5 Backbone

**Backbone이란** 이미지로 부터 Feature map 즉 **특징을 추출하는 부분**

Yolov5는 **CSPNe**t을 사용함

CSPNet은 Neural Network가 점점 깊어지면서, 네트워크 연산량이 많아지며 모델이 무거워지는 것을 방지하기 위해

Gradient combination이 만들어지는 동안 **연산량을 줄이는 것이 목적**

### CSPNet

1.Input Data 분할: 먼저 입력 데이터가 CSPNet으로 들어오면, 입력 데이터는 두 개의 파트로 분할됩니다. 이 두 파트는 저수준(low-level)과 고수준(high-level) 특징을 처리하기 위한 것입니다. 이 과정에서 Scale Variation의 개념도 도입됩니다. 각 파트는 서로 다른 레이어 스테이지로 연결되며, 다양한 크기의 객체를 고려하도록 설계됩니다.

2.저수준 특징 처리(미세한 특징 추출): 첫 번째 파트에서는 입력 데이터의 일부분을 컨볼루션 연산을 통해 저수준 특징을 추출합니다. 이 과정에서 Partial Convolution 개념이 적용됩니다. Partial Convolution은 입력 데이터의 부분적인 영역만을 활용하여 컨볼루션 연산을 수행하므로, 계산량을 줄이면서도 필요한 정보를 보존합니다. 이 부분에서는 작은 객체나 이미지의 세부 정보와 관련된 특징을 포착하려고 합니다.

3.고수준 특징 처리(큰 특징 추출): 두 번째 파트에서는 고수준 특징을 추출하기 위해 입력 데이터의 나머지 부분을 컨볼루션 연산을 통해 처리합니다. 이 부분에서도 Partial Convolution을 적용하여 계산량을 줄이면서도 필요한 정보를 추출합니다. 이 단계에서는 물체의 전체 모양, 구조 등을 포착하려고 합니다.

4.Cross Stage Connection 및 결합: 이후에는 Cross Stage Connection이 이루어지며, 저수준과 고수준 특징을 결합합니다. 이를 통해 낮은 수준과 고수준의 특징을 효과적으로 혼합하여 보다 풍부한 정보를 얻을 수 있습니다.

- 낮은 수준-> 작은 픽셀이라고 생각하면 됨(세부적인 정보를 담고 있음)
- 고수준-> 큰 픽셀이라고 생각하면 됨(전체적인 모양과 구조를 파악하는데 사용함)

5.출력 계산: 최종적으로 Cross Stage Connection을 통해 결합된 특징을 이용하여 네트워크의 나머지 부분으로 전달하고, 필요한 후처리나 손실 함수를 이용하여 최종 출력을 계산합니다.

이렇게 Partial Convolution과 Scale Variation 개념이 CSPNet의 학습 순서에 포함되며, 이들은 네트워크의 성능 향상과 효율적인 정보 처리를 위해 중요한 역할을 합니다.

![image](https://github.com/eumtaewon/Yolov5-Review/assets/104436260/bb4eda4b-6b6a-42ed-b6dd-e7cf5a7654da)
   





## Yolov5 Head

Backbone에서 추출된 Feature map을 바탕으로 물체의 위치(바운딩 박스)를 찾는 부분

총 3가지 크기에서 바운딩 박스를 생성함(8pixel, 16pixel, 32pixel)

각 픽셀에서 3개의 앵커 박스를 사용하여 예측함








#  YOLOv5 Model을 활용하여 실제 객체 탐지 pilot 분석 진행 

실제 수집한 데이터 샘플로 YOLO5 구현

yolov5s 모델 사용해서 학습 진행

사용한 데이터

- 이미지 데이터

- 이미지 데이터의 메타정보가 포함된 라벨링 데이터

- 이미지와 라벨링 데이터는 한쌍으로 이루어져 있음

# Index

1.Import Library

2.Data Preprocessing

3.Model Training

4.Model Test

5.Result

# Import Library

![image](https://user-images.githubusercontent.com/104436260/208832899-402b4986-e5e7-44ee-99d8-592dc2cc28ea.png)

패키지 load

# Data Preprocessing

![image](https://user-images.githubusercontent.com/104436260/208833062-4f258a3b-97c6-45f2-a0ae-9985c02b547f.png)

각 Class별 객체가 최소 몇개 있는지 확인해주는 함수 

D_Load 함수는 소스 코드에서 확인.

![image](https://user-images.githubusercontent.com/104436260/208833841-84ea1657-02b5-45d2-b096-6304a5f9bd50.png)

Data Split 비율은 Train:Valid:Test=8:1:1

이후에, 원천데이터를 Train, Valid, Test 폴더를 각각 만들어 이미지와 라벨링 데이터를 shutil.copy함수를 통해 복사 붙여넣기 해줌

![image](https://user-images.githubusercontent.com/104436260/208834542-13cfd717-1e13-46ca-b943-e043f7eb5f76.png)

Valid와 Test 데이터 셋도 똑같이 새로운 폴더에 복사 붙여넣기 해줌

![image](https://user-images.githubusercontent.com/104436260/208835889-187ff12e-4e79-4ba0-aeb3-d49c7c14a82c.png)

Train data의 개수는 총 16800개(음성, 양성 각각 8400장)

# Train data folder 구조 변경하기

원래 Train 이미지 data folder구조: train->원천데이터->실제데이터->Bounding Box->생애이슈(음성) or 생애이슈(양성)

원래 Train 라벨링 data folder구조: train->라벨링데이터->실제데이터->Bounding Box->생애이슈(음성) or 생애이슈(양성)

바뀐 Train 이미지 data folder 구조: train->images

바뀐 Train 라벨링 data folder 구조: train->labels

Validation과 Test 데이터 셋의 구조도 Train과 같은 형태로 바꿔줌

![image](https://user-images.githubusercontent.com/104436260/208844006-176a66fd-df50-4eb8-9151-e4a85e732284.png)

# Yolov5 모델에 맞게 라벨링 데이터 수정

![image](https://user-images.githubusercontent.com/104436260/208846703-2fc1362a-356a-4517-af65-bd0414e5cec5.png)


라벨값, X_center, Y_center, Width, Height 순으로 라벨데이터 변환

# Train Valid text file 작성하기

![image](https://user-images.githubusercontent.com/104436260/208852409-e537df93-6675-4dbd-8030-548e0207375d.png)

train data와 valid data의 경로가 적혀있는 txt파일 생성

# data.yaml 파일에 train, validation data 경로 입력

![image](https://user-images.githubusercontent.com/104436260/208854019-c6dcf60f-43a8-4c9f-b79a-92e2b2a964b0.png)

# Model Training

![image](https://user-images.githubusercontent.com/104436260/208855094-05e50988-7ac7-4af0-a106-3b696c323474.png)

위의 주소로 git clone 해주고

requirements.txt에 있는 패키지들을 모두 깔아줌

캡쳐본과 같이 파라미터들 설정하고 모델 트레이닝 진행

# 학습결과 확인

![image](https://user-images.githubusercontent.com/104436260/209028017-bdd5466f-d501-4153-88b0-a05b5d5c797d.png)

첫 번째 학습 배치에 포함된 정답 이미지


