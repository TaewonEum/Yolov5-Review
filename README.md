# bee_detection_project

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

