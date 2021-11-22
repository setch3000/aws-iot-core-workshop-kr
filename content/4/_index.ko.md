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

Resources 항목에서는 ***All resources***를 선택합니다.

{{% notice info %}}
하지만, 실제 운용을 위해서는 best security practices를 위해 client, topic, topicfilter를 제한하십시오.
{{% /notice %}}

---
<p align="center">
© 2021 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
</p>
