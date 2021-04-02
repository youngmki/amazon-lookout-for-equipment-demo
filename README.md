# Amazon Lookout for Equipment 데모
Amazon Lookout for Equipment는 센서 데이터를 사용하여 장비의 비정상적인 동작을 감지하므로 기계 고장이 발생하기 전에 조치를 취하고 뜻밖의 가동 중지 시간을 방지할 수 있습니다.

**참고:** *모델 훈련 시간 55분을 포함하여 노트북 전체를 실행하는 데 약 1.5시간이 소요됩니다.*

## 개요
Amazon Lookout for Equipment는 압력, 유속, RPM, 온도 및 전력과 같은 센서 데이터를 분석하여 ML 전문 지식 없이 장비에 대한 데이터만을 기반으로 특정 ML 모델을 자동으로 훈련시킵니다. Lookout for Equipment는 고유한 ML 모델을 사용하여 입력되는 센서 데이터를 실시간으로 분석하고 기계 고장으로 이어질 수있는 조기 경고 신호를 정확하게 식별합니다. 즉 장비 이상을 빠르고 정확하게 감지하고, 문제를 신속하게 진단하고, 큰 비용이 드는 가동 중지 시간을 줄이기 위한 조치를 취하고, 잘못된 경고를 줄일 수 있습니다.

### 설치 가이드
아직 계정이 없다면 [**AWS 계정을 먼저 생성해야 합니다.**](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html) 현재 해당 서비스는 Preview로 제공되고 있습니다. 그러므로 데모를 실행하려면 AWS 계정이 허용 목록에 포함되어야 합니다. Preview를 신청하려면 [**이 링크**](https://pages.awscloud.com/Amazon-Lookout-for-Equipment-Preview.html)를 따라가세요.

서비스에 대한 접근 권한이 있으면 AWS 계정에 접속한 후 SageMaker 콘솔로 이동하여 새 인스턴스를 생성합니다. 데이터셋을 안정적으로 처리하고 시각화하려면 5GB EBS 볼륨표준이 연결된 **ml.m5.xlarge 인스턴스** 를 사용하는 것이 좋습니다. 대용량 시계열 데이터셋을 탐색하려면 EBS 볼륨 크기를 키워야 할 수 있습니다. 일부 플롯은 상당한 크기의 메모리를 요구할 수 있습니다. 탐색 과정 도중에 더 큰 크기의 메모리 최적화 인스턴스(예: **ml.m5.4xlarge**)로 바꿔 종종 실행하기도 합니다.

본인의 노트북 인스턴스에 Amazon Lookout for Equipment API를 호출 할 수 있는 **IAM 역할**이 있는지 확인해야 합니다.

1. 노트북 인스턴스가 양도할 SageMaker 실행 역할을 **IAM 콘솔**에서 찾아보세요. (`AmazonSageMaker-ExecutionRole-yyyymmddTHHMMSS`과 같은 이름을 가진 역할)
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
4. 해당 역할의 `Trust relationship` 탭으로 이동하여 `Edit trust relationship` 버튼을 클릭하고 다음과 같은 정책을 입력하세요. 해당 역할에 대해 이미 신뢰 관계가 있는 경우 서비스 목록에 **"lookoutequipment.amazonaws.com"** 을 추가하기만 하면 됩니다.

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
5. `Update the Trust Policy` 버튼을 클릭하세요. SageMaker 노트북 인스턴스는 이제 Lookout for Equipment API를 호출 할 수 있으며 서비스는 데이터가있는 S3 버킷에 대한 적절한 접근 권한을 갖게됩니다. 

Amazon SageMaker 콘솔로 다시 돌아간 다음 노트북 인스턴스 메뉴로 돌아갈 수 있습니다. 인스턴스를 시작하고 Jupyter 또는 JupyterLab 세션을 시작합니다. 여기서 새 터미널을 시작하고 `git clone`를 통해 본 저장소를 당신의 로컬 개발 환경으로 복제하세요.

### 저장소 구조
이 저장소를 복제한 다음 [**데이터 준비**](notebooks/1_data_preparation.ipynb) 노트북으로 이동합시다. 이 첫 번째 노트북은 다른 노트북에 필요한 데이터를 다운로드하고 준비합니다.

```
.
|
+-- README.md                          <-- 본 가이드 파일
|
+-- assets/                            <-- 노트북에서 사용되는 그림들
|
+-- dataset/                           <-- Raw dataset files are located here
|   |-- labels.csv                         | Known anomaly ranges
|   |-- tags_description.csv               | Description of each time serie signal
|   |-- timeranges.txt                     | Train / evaluation split
|   \-- timeseries.zip                     | Raw timeseries
|
+-- notebooks/
|   |-- 1_data_preparation.ipynb       <-- START HERE: data preparation notebook, useful to
|   |                                      download and prepare the data, get familiar with
|   |                                      them
|   |
|   |-- 2_dataset_creation.ipynb       <-- Create a Lookout for Equipment dataset
|   |-- 3_model_training.ipynb         <-- Train a Lookout for Equipment model
|   |-- 4_model_evaluation.ipynb       <-- Plot the evaluation results and some diagnostics
|   \-- 5_inference_scheduling.ipynb   <-- Schedule a regular inference execution
|
+-- utils/
    |-- lookout_equipment_utils.py     <-- Utilities to manage Lookout for Equipment assets
    \-- lookoutequipment.json          <-- Configuration file to access the API while the service
                                           is in preview
```

## Questions

Please contact [**Michaël HOARAU**](mailto:michoara@amazon.fr) or raise an issue on this repository.

## Security

See [**CONTRIBUTING**](CONTRIBUTING.md#security-issue-notifications) for more information.

## License
This collection of notebooks is licensed under the MIT-0 License. See the LICENSE file.
