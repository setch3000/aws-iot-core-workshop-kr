---
title: Device Advisor
weight: 50
pre: "<b>4. </b>"
---

## 디바이스 역할로 사용할 IAM Rol 생성

[IAM > Policies 콘솔](https://console.aws.amazon.com/iamv2/home?#/policies)로 이동하고 'Create policy'을 클릭합니다.

Service 항목에는 ***IoT***를 선택합니다.
![2.png](/images/4/2.png)

Actions 항목을 추가합니다. 먼저, 검색창에 ```Connect```로 검색을 하시면, 아래와 같이 ***Connect***항목을 찾으실 수 있습니다. 해당 항목을 체크합니다.
![3.png](/images/4/3.png)

같은 방식으로 검색창에 ```Publish```로 검색을 한 후, ***Connect***항목을 체크합니다.
![4.png](/images/4/4.png)

같은 방식으로 검색창에 ```Subscribe```로 검색을 한 후, ***Subscribe***항목을 체크합니다.

![5.png](/images/4/5.png)

같은 방식으로 검색창에 ```Receive```로 검색을 한 후, ***Receive***항목을 체크합니다.

![6.png](/images/4/6.png)

Resources 항목에는 ***All Resouces***를 선택합니다.
하지만, Best Practice로써 Specific을 선택하고 꼭 필요한 리소스에만 policy가 적용될 수 있도록 하는 것을 추천드립니다.
***Next: Tags***를 클릭하여 다음 단계로 이동합니다.

![7.png](/images/4/7.png)

***Next: Review***를 클릭하여 다음 단계로 이동합니다.

![8.png](/images/4/8.png)

Name에 ```MyIoTDeviceAdvisorPolicy```를 입력하고 ***Create policy***를 클릭하여 policy 생성을 완료합니다.

![9.png](/images/4/9.png)

[IAM > Roles 콘솔](https://console.aws.amazon.com/iamv2/home#/roles)로 이동 후 'Create role'을 클릭합니다.

***IoT*** 서비스를 선택하고 Select your use case에서도 ***IoT***를 선택합니다.
![11.png](/images/4/11.png)

***Next: Tags***를 클릭하여 다음 단계로 이동합니다.
![12.png](/images/4/12.png)

***Next: Review***를 클릭하여 다음 단계로 이동합니다.
![13.png](/images/4/13.png)

Role Name에 ```MyIoTDeviceAdvisorRole```를 입력하고 ***Create role***를 클릭하여 role 생성을 완료합니다.
![14.png](/images/4/14.png)


위에서 생성하신 [MyIoTDeviceAdvisorRole](https://console.aws.amazon.com/iam/home#/roles/MyIoTDeviceAdvisorRole
) Role로 이동하고 ***Attach policies***를 클릭합니다.
![15.png](/images/4/15.png)

Filter polices에 위에서 생성하신 ```MyIoTDeviceAdvisorPolicy```를 검색한 후 체크합니다.
![16.png](/images/4/16.png)

***Edit trust relationship***을 클릭합니다.
![17.png](/images/4/17.png)

Policy Document에 아래 Json을 붙여 넣어줍니다. 그리고, ***Update Trust Policy***를 클릭합ㄴ디ㅏ.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowAwsIoTCoreDeviceAdvisor",
      "Effect": "Allow",
      "Principal": {
        "Service": "iotdeviceadvisor.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

![18.png](/images/4/18.png)

---
<p align="center">
© 2021 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
</p>
