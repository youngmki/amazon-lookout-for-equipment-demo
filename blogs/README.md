# Amazon Lookout for Equipment와 Amazon A2I를 사용하여 비정상적인 장비 동작 탐지를 자동화하고 전체 과정 반복하면서 작업자와 함께 예측값 검토하기
---
본 블로그에서는 예지 정비를 위해 풍력 터빈 데이터셋으로 비정상 동작 탐지 모델을 훈련하도록 Amazon Lookout for Equipment를 설정하고 반복 작업 흐름 도중 지정된 작업자가 Amazon A2I를 사용하여 예측값을 검토하고 데이터셋을 보강하고 모델을 재훈련하는 일련의 과정을 보여줍니다. Amazon Lookout for Equipment를 시작하여 데이터셋을 생성하고, 데이터를 수집하고, 모델을 훈련하고, 스케줄러를 설정하여 추론을 실행할 것입니다. 이 단계를 통과하면 Amazon A2I를 사용하여 작업자 검토 프로세스를 신속하게 설정하여 보강한 데이터 또는 작업자가 검토한 데이터셋으로 모델을 재훈련하는 방법을 보여줍니다.

In this blog we will show you how you can setup Amazon Lookout for Equipment to train an abnormal behavior detection model using a wind turbine dataset for predictive maintenance and setup up a human in the loop workflow to review the predictions using Amazon A2I, augment the dataset and retrain the model. To get started with Amazon Lookout for Equipment, we will create a dataset, ingest data, train a model and run inference by setting up a scheduler. After going through
these steps we will show you how you can quickly setup human review process using Amazon A2I and retrain your model with augmented or human reviewed datasets.

We will walk you through the following steps: 
1. Creating a dataset in Amazon Lookout for Equipment
2. Ingesting data into the Amazon Lookout for Equipment dataset
3. Training a model in Amazon Lookout for Equipment
4. Running diagnostics on the trained model
5. Creating an inference scheduler in Amazon Lookout for Equipment to send a simulated stream of real-time requests
6. Setting up an Amazon A2I private human loop and reviewing the predictions from Amazon Lookout for Equipment
7. Retraining your Amazon Lookout for Equipment model based on augmented datasets from Amazon A2I

Please follow the instructions in the [accompanying blog post](https://aws.amazon.com/blogs/machine-learning/detect-abnormal-equipment-behavior-and-review-predictions-using-amazon-lookout-for-equipment-and-amazon-a2i/) to clone this repository and get started with your Jupyter notebook
