# Amazon Lookout for Equipment와 Amazon A2I를 사용하여 비정상적인 장비 동작 탐지를 자동화하고 전체 과정 반복하면서 작업자와 함께 예측값 검토하기
---
본 블로그에서는 예지 정비를 위해 풍력 터빈 데이터셋으로 비정상 동작 탐지 모델을 훈련하도록 Amazon Lookout for Equipment를 설정하고 반복 작업 흐름 도중 지정된 작업자가 Amazon A2I를 사용하여 예측값을 검토하고 데이터셋을 보강하고 모델을 재훈련하는 일련의 과정을 보여줍니다. Amazon Lookout for Equipment를 시작하여 데이터셋을 생성하고, 데이터를 수집하고, 모델을 훈련하고, 스케줄러를 설정하여 추론을 실행할 것입니다. 이 단계를 진행하고나면 Amazon A2I를 사용하여 작업자 검토 프로세스를 신속하게 설정하여 보강된 데이터 또는 작업자가 검토한 데이터셋으로 모델을 재훈련하는 방법을 보여줍니다.

다음 단계를 진행할 것입니다.
1. Amazon Lookout for Equipment에서 데이터셋 생성하기
2. Amazon Lookout for Equipment 데이터셋으로 데이터 수집하기
3. Amazon Lookout for Equipment에서 모델 훈련하기
4. 훈련한 모델에서 진단 실행하기
5. Amazon Lookout for Equipment에서 추론 스케줄러를 생성하여 모의 생성한 실시간 요청 스트림을 전송합니다.
6. Amazon A2I 프라이빗 휴먼 루프 설정하고 Amazon Lookout for Equipment로 예측 검토하기
7. Amazon A2I의 보강 데이터셋을 기반으로 Amazon Lookout for Equipment 모델 재훈련하기

[첨부한 블로그 게시물] (https://aws.amazon.com/blogs/machine-learning/detect-abnormal-equipment-behavior-and-review-predictions-using-amazon-lookout-for- equipment-and-amazon-a2i /)를 사용하여이 저장소를 복제하고 Jupyter 노트북을 시작합니다.

Please follow the instructions in the [accompanying blog post](https://aws.amazon.com/blogs/machine-learning/detect-abnormal-equipment-behavior-and-review-predictions-using-amazon-lookout-for-equipment-and-amazon-a2i/) to clone this repository and get started with your Jupyter notebook
