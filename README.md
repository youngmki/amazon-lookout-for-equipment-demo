# Amazon Lookout for Equipment 데모
Amazon Lookout for Equipment는 센서 데이터를 사용하여 장비의 비정상적인 동작을 감지하므로 기계 고장이 발생하기 전에 조치를 취하고 뜻밖의 가동 중지 시간을 미연에 방지할 수 있습니다.

**참고:** *모델 훈련 시간 55분을 포함하여 노트북 전체를 실행하는 데 약 1.5시간이 소요됩니다.*

## 개요
Amazon Lookout for Equipment는 압력, 유속, RPM, 온도 및 전력량과 같은 센서 데이터를 분석하여 ML 전문 지식 필요 없이 장비 데이터만 가지고 특정 ML 모델을 자동으로 훈련시킵니다. Lookout for Equipment는 생성한 ML 모델을 사용하여 입력되는 센서 데이터를 실시간으로 분석하고 기계 고장으로 이어질 수 있는 조기 경고 신호를 정확하게 식별합니다. 즉 장비 이상을 빠르게, 정확하게 감지하고 문제를 신속하게 진단하고 상당한 비용이 소요되는 가동 중지 시간을 줄이기 위해 조치를 취하고 잘못된 경고를 줄일 수 있습니다.

### 설치 가이드
아직 계정이 없다면 [**AWS 계정을 먼저 생성해야 합니다.**](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html) 현재 해당 서비스는 Preview로 제공되고 있습니다. 그러므로 데모를 실행하려면 Preview를 신청하여 AWS 계정이 허용 목록에 포함되어야 합니다. Preview를 신청하려면 [**이 링크**](https://pages.awscloud.com/Amazon-Lookout-for-Equipment-Preview.html)를 따라가세요.

서비스에 대한 접근 권한이 있다면 AWS 계정에 접속한 다음 SageMaker 콘솔로 이동하여 새 인스턴스를 생성합니다. 데이터셋을 안정적으로 처리하고 시각화하려면 5GB EBS 볼륨 표준이 연결된 **ml.m5.xlarge 인스턴스**를 사용할 것을 권장합니다. 대규모 시계열 데이터셋을 탐색하려면 EBS 볼륨을 키워야 할 수도 있습니다. 일부 플롯은 상당한 크기의 메모리를 요구합니다. 탐색 과정 도중 더 큰 메모리의 최적화 인스턴스(예: **ml.m5.4xlarge**) 로 바꾸어 진행하기도 합니다.

자신의 노트북 인스턴스에 Amazon Lookout for Equipment API를 호출할 수 있는 **IAM 역할**이 있는지 확인해야 합니다.

1. 노트북 인스턴스가 위임할 SageMaker 실행 역할을 **IAM 콘솔**에서 찾아보세요. (`AmazonSageMaker-ExecutionRole-yyyymmddTHHMMSS` 같은 이름을 가진 역할)
2. `Permissions` 탭에서 `Add inline policy`을 클릭한 후 `JSON`을 선택하고 다음과 같은 정책을 입력합니다.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "lookoutequipment:*",
            "Resource": "*"
        },
        {
            "Action": "iam:PassRole",
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```
3. `Review policy` 버튼을 클릭하고 `Create policy` 버튼을 클릭하기 전에 이름을 지정하세요. (예: **LookoutEquipmentAccess**)
4. 해당 역할의 `Trust relationship` 탭으로 이동하여 `Edit trust relationship` 버튼을 클릭하고 다음과 같은 정책을 입력하세요. 해당 역할에 대해 이미 신뢰 관계가 있는 경우 서비스 목록에 **"lookoutequipment.amazonaws.com"**\을 추가하기만 하면 됩니다.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": [
          "sagemaker.amazonaws.com",
          "lookoutequipment.amazonaws.com"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
5. `Update the Trust Policy` 버튼을 클릭하세요. SageMaker 노트북 인스턴스는 이제 Lookout for Equipment API를 호출할 수 있으며 서비스는 데이터가 있는 S3 버킷에 대해 적절한 접근 권한을 갖게 됩니다. 

Amazon SageMaker 콘솔로 다시 돌아간 다음 노트북 인스턴스 메뉴로 이동합니다. 인스턴스를 시작하고 Jupyter 또는 JupyterLab 세션을 시작합니다. 새 터미널을 시작한 다음 `git clone`를 이용하여 본 저장소를 로컬 개발 서버로 복제합니다.

### 저장소 구조
본 저장소를 복제한 다음 [**데이터 준비**](notebooks/1_data_preparation.ipynb) 노트북으로 이동합니다. 이 첫 번째 노트북은 다른 노트북에 필요한 데이터를 다운로드하여 준비합니다.

```
.
|
+-- README.md                          <-- 본 가이드 파일
|
+-- assets/                            <-- 노트북에서 사용되는 사진들
|
+-- dataset/                           <-- 원천 데이터셋 파일이 여기에 있습니다
|   |-- labels.csv                         | 알려진 이상 범위
|   |-- tags_description.csv               | 각 신호 시계열에 대한 설명
|   |-- timeranges.txt                     | 훈련 / 검증 분할
|   \-- timeseries.zip                     | 원천 시계열
|
+-- notebooks/
|   |-- 1_data_preparation.ipynb       <-- 여기서 시작: 데이터 다운로드와 준비에 필요한
|   |                                      데이터 준비 노트북이므로 잘 살펴보세요
|   |                                      
|   |
|   |-- 2_dataset_creation.ipynb       <-- Lookout for Equipment 데이터셋 생성
|   |-- 3_model_training.ipynb         <-- Lookout for Equipment 모델 훈련
|   |-- 4_model_evaluation.ipynb       <-- 평가 결과와 일부 진단 내용을 시각화
|   \-- 5_inference_scheduling.ipynb   <-- 정기적인 추론 작업 실행 예약
|
+-- utils/
    |-- lookout_equipment_utils.py     <-- Lookout for Equipment 자산을 관리하는 유틸리티
    \-- lookoutequipment.json          <-- 서비스가 Preview 상태인 동안 API에 접근하기 위한 구성 파일
                                          
```

## 문의
[**Michaël HOARAU**](mailto:michoara@amazon.fr)에게 연락하거나 저장소에 Issue를 올리세요.

## 보안
자세한 내용은 [**기여하기**](CONTRIBUTING.md#security-issue-notifications)를 참조하세요.

## 라이선스
MIT-0 라이선스에 의거하여 본 노트북 모음에 라이선스가 부여됩니다. LICENSE 파일을 참조하세요.
