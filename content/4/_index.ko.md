---
title: Device Advisor
weight: 50
pre: "<b>4. </b>"
---

## Device Advisor를 위한 policy 생성

[IAM > Policies 콘솔](https://console.aws.amazon.com/iamv2/home#/policies)로 이동하고 ***Create Policy***를 클릭합니다.

Service 항목에는 ***IoT***를 선택합니다.
![2.png](/images/4/2.png)

Action 항목에는 아래 네 개 항목을 Filter actions창에서 검색하고 선택합니다.

+ Connect
+ Publish
+ Subscribe
+ Receive

![3.png](/images/4/3.png)
![4.png](/images/4/4.png)
![5.png](/images/4/5.png)
![6.png](/images/4/6.png)

Resources 항목에서는 ***All resources***를 선택하고, ***Next: Tags***를 선택합니다.

{{% notice info %}}
하지만, 실제 운용을 위해서는 best security practices를 위해 client, topic, topicfilter를 제한하십시오.
{{% /notice %}}

다음 화면에서 ***Next: Review***를 선택합니다. 

![8.png](/images/4/8.png)

***Name*** 항목에 ```MyIoTDeviceAdvisorPolicy```를 입력하고 ***Create policy***를 클릭합니다.
![9.png](/images/4/9.png)


[IAM > Roles 콘솔](https://console.aws.amazon.com/iamv2/home#/roles)로 이동하고 ***Create role***를 클릭합니다.

***Or select a service to view its use cases*** 항목에서 ***IoT***를 선택합니다.
***Select your use case*** 항목에서도 ***IoT***를 선택하고 ***Next: Permissions***을 클릭합니다.

![11.png](/images/4/11.png)


다음 단계에서 ***Next: Tags***를 클릭합니다.

![12.png](/images/4/12.png)

다음 단계에서 ***Next: Review***를 클릭합니다.

![13.png](/images/4/13.png)

Role name에 ```MyIoTDeviceAdvisorRole```을 입력하고 ***Create role***을 클릭하여 role 생성을 완료합니다.
![14.png](/images/4/14.png)

방금 생성한 [MyIoTDeviceAdvisorRole](https://console.aws.amazon.com/iam/home#/roles/MyIoTDeviceAdvisorRole)로 이동하고 ***Attach policies***를 클릭합니다.
![15.png](/images/4/15.png)

이전 단계에서 생성한 ```MyIoTDeviceAdvisorPolicy```를 검색하여 선택하고 ***Attach policy***를 클릭합니다.
![16.png](/images/4/16.png)

***Trust relationships***을 클릭하고 ***Edit trust relationship***을 클릭합니다.
![17.png](/images/4/17.png)

***Policy Document***를 아래 내용으로 교체하고 ***Update Trust Policy***를 클릭합니다.

``` json
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
