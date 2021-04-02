# Amazon Lookout for Equipment 데모
Amazon Lookout for Equipment는 센서 데이터를 사용하여 장비의 비정상적인 동작을 감지하므로 기계 고장이 발생하기 전에 조치를 취하고 뜻밖의 가동 중지 시간을 방지할 수 있습니다.

**참고:** *모델 훈련 시간 55분을 포함하여 노트북 전체를 실행하는 데 약 1.5시간이 소요됩니다.*

## 개요
Amazon Lookout for Equipment는 압력, 유속, RPM, 온도 및 전력과 같은 센서 데이터를 분석하여 ML 전문 지식 없이 장비에 대한 데이터만을 기반으로 특정 ML 모델을 자동으로 훈련시킵니다. Lookout for Equipment는 고유한 ML 모델을 사용하여 입력되는 센서 데이터를 실시간으로 분석하고 기계 고장으로 이어질 수있는 조기 경고 신호를 정확하게 식별합니다. 즉 장비 이상을 빠르고 정확하게 감지하고, 문제를 신속하게 진단하고, 큰 비용이 드는 가동 중지 시간을 줄이기 위한 조치를 취하고, 잘못된 경고를 줄일 수 있습니다.

### 설치 가이드
아직 계정이 없다면 [**AWS 계정을 먼저 생성해야합니다.**](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html) 현재 해당 서비스는 Preview로 제공되고 있습니다. 그러므로 데모를 실행하려면 AWS 계정이 허용 목록에 포함되어야 합니다. Preview를 신청하려면 [**이 링크**](https://pages.awscloud.com/Amazon-Lookout-for-Equipment-Preview.html)를 따라가세요.

서비스에 대한 액세스 권한이 있으면 AWS 계정에 로그인하고 SageMaker 콘솔로 이동하여 새 인스턴스를 생성합니다. 데이터 세트를 편안하게 처리하고 시각화하려면 표준 5GB EBS 볼륨이 연결된 **ml.m5.xlarge 인스턴스** 를 사용하는 것이 좋습니다. 큰 시계열 데이터 세트를 탐색하려면 EBS 볼륨 크기를 늘려야 할 수 있습니다. 일부 플롯은 상당한 양의 메모리를 차지할 수 있습니다. 이러한 탐색에서 더 큰 메모리 최적화 인스턴스 (예 : **ml.m5.4xlarge**)로 이동하는 것은 드문 일이 아닙니다 .

You need to ensure that this notebook instance has an **IAM role** which allows it to call the Amazon Lookout for Equipment APIs:

1. In your **IAM console**, look for the SageMaker execution role endorsed by your notebook instance (a role with a name like `AmazonSageMaker-ExecutionRole-yyyymmddTHHMMSS`)
2. On the `Permissions` tab click on `Add inline policy`, select `JSON` and enter the following policy:

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
3. Click on `Review policy` and name it (e.g. **LookoutEquipmentAccess**) before clicking on `Create policy`
4. Browse to the `Trust relationship` tab for this role, click on the `Edit trust relationship` button and fill in the following policy. You may already have a trust relationship in place for this role, in this case, just add the **"lookoutequipment.amazonaws.com"** in the service list:

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
5. Click on `Update the Trust Policy`: your SageMaker notebook instance can now call the Lookout for Equipment APIs and the service will have the appropriate access to the S3 buckets where the data will be located.

You can know navigate back to the Amazon SageMaker console, then to the Notebook Instances menu. Start your instance and launch either Jupyter or JupyterLab session. From there, you can launch a new terminal and clone this repository into your local development machine using `git clone`.

### Repository structure
Once you've cloned this repo, browse to the [**data preparation**](notebooks/1_data_preparation.ipynb) notebook: this first notebook will download and prepare the data necessary for the other ones.

```
.
|
+-- README.md                          <-- This instruction file
|
+-- assets/                            <-- Pictures used throughout the notebooks
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
