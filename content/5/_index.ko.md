---
title: Cognito를 통한 IoT Core 활용
weight: 60
pre: "<b>5. </b>"
---

<!-- ## Cloud9의 디스크 용량 늘리기


[AWS IoT > Policies > Create a policy](https://console.aws.amazon.com/ec2/v2/home#Volumes:sort=desc:createTime)로 이동합니다.

사이즈를 20으로 변경합니다.

Cloud9의 터미널에서 아래 명령을 입력합니다.

``` shell
sudo growpart /dev/nvme0n1 1
sudo resize2fs /dev/nvme0n1p1
```

```df -hT```명령으로 확인합니다. -->

## Cognito User Pool 생성

[Cognito 콘솔](https://console.aws.amazon.com/cognito/home)로 이동합니다.

***Manage User Pools***를 클릭합니다.

![1.png](/images/5/1.png)

***Create a user pool***를 클릭합니다.

![2.png](/images/5/2.png)

Pool name에 ```iot-pool```을 입력하고, ***Review defaults***를 클릭합니다.

![3.png](/images/5/3.png)

***Add app client***를 클릭하여, App clients 메뉴로 이동합니다.

![4.png](/images/5/4.png)

***Add an app client***를 클릭합니다.

![5.png](/images/5/5.png)

App client name에 ```iot-app-client```를 입력하고, Generate client secret을 ***uncheck*** 합니다.
그리고, ***Create app client*** 버튼을 클릭하여 app client를 생성합니다.

![6.png](/images/5/6.png)

***Return to pool datails***를 클릭합니다.

![7.png](/images/5/7.png)

***Create pool***을 클릭하여, user pool 생성을 완료합니다.

![8.png](/images/5/8.png)

User pool 생성이 완료되면 ***Pool Id***의 값을 복사하여 둡니다. 이것은 이후 설정에서 사용됩니다.

![9.png](/images/5/9.png)

App client settings 메뉴를 클릭하고 App client의 ***ID***의 값도 복사하여 둡니다. 이것은 이후 설정에서 사용됩니다.

![10.png](/images/5/10.png)


## Cognito Identity Pool 생성

좌측 상단에서 ***Federated Identities***를 클릭합니다.

![11.png](/images/5/11.png)

좌측 상단에서 ***Create new identity pool***을 클릭합니다.

![12.png](/images/5/12.png)

***Identity pool name***에는 ```iot identity pool```을 입력하시고
***User Pool ID***에는 이전 단계에서 복사한 User Pool의 Id를 입력합니다.
***App client id***에도 이전 단계에서 복사해 두었던 app client의 Id를 입력합니다.
***Create***를 클릭하여 생성을 완료합니다. 

![13.png](/images/5/13.png)


Policy Document를 Edit합니다. 콤마(,)를 추가하고 다음의 action들을 추가합니다.

```
"iot:AttachPrincipalPolicy",
"iot:AttachThingPrincipal",
"iot:Connect",
"iot:Publish",
"iot:Subscribe",
"iot:Receive",
"iot:GetThingShadow"
```

우측 하단에 있는 ***Allow***를 클릭합니다.

![14.png](/images/5/14.png)


## Cognito Identity 에 사용할 IoT Policy 생성

[AWS IoT > Policies > Create a policy](https://console.aws.amazon.com/iot/home?#/create/policy)로 이동합니다.

Name에 ```congito-iot-policy```을 입력하고,
Action에 ```iot:*``` 을 입력합니다.
Resource ARN에서 replaceWithATopic 부분을 '*'로 변경하여 줍니다.
Effect에서 ***Allow***를 체크해 줍니다.

![15.png](/images/5/15.png)



## App integration 설정

[Cognito > User Pools](https://console.aws.amazon.com/cognito/users/)로 이동합니다.

이전에 생성한 ***iot-pool***을 선택하고 App client settings를 선택합니다.
App client settings에서 다음을 설정합니다.
+ Enabled Identity Providers에서 ***Select all***을 체크
+ Callback URL(s)와 Sign out URL(s)에 ```http://localhost:8080```을 입력
+ Allowed OAuth Flows에서 ***Authorization code grant***와 ***Implicit grant***를 체크
+ Allowed OAuth Scopes에서 ***email***과 ***openid***를 체크

***Save changes***를 클릭하여 설정을 저장합니다.

![16.png](/images/5/16.png)


Domain name에서 ***iot-[AWS account number]***형식으로 입력을 합니다.
***Check availability***를 클릭하여 중복 여부를 확인합니다.
***Save changes***를 클릭하여 설정을 저장합니다.

![17.png](/images/5/17.png)


```
wget resize.sh
chmod +x resize.sh
./resize.sh 20
df -hT
```

## AWS IoT Device SDK JS 설치

다음과 같은 명령으로 Node.js가 정상적으로 설치되어 있는지 확인합니다. 

``` shell
node -v
```

다음과 같은 명령으로 IoT Device SDK JS를 설치합니다.

``` shell
git clone https://github.com/aws/aws-iot-device-sdk-js.git
cd aws-iot-device-sdk-js
npm install
```


## Sample code 다운로드 및 실행

하기 명령을 통하여 sample code를 다운로드하고 unzip합니다.

``` shell
wget https://sehyul.s3.ap-northeast-2.amazonaws.com/iot-core/cognito-sample.zip
unzip cognito-sample.zip
cd sample

```

sample 폴더에서 index.html을 열고 편집합니다.

App Client ID, Amazon Cognito domain, Callback URL, Sign out UR, User Pool ID 등의 정보를 source code에 매핑하여 줍니다.

![21.png](/images/5/21.png)

Cognito Identity Pool ID의 정보를 source code에 매핑하여 줍니다.

![22.png](/images/5/22.png)


다음 명령으로 IoT Core의 endpoint를 확인하고

``` shell
aws iot describe-endpoint \
    --endpoint-type iot:Data-ATS
```

![23.png](/images/5/23.png)


해당 정보를 source code에 매핑합니다.

![24.png](/images/5/24.png)


다음 명령으로 http server를 실행합니다. port는 8080 으로 지정합니다.

```
python3 -m http.server 8080
```

Tools > Preview > Preview Running Application 메뉴를 클릭합니다.

![25.png](/images/5/25.png)


수고하셨습니다.

References
https://docs.amplify.aws/sdk/pubsub/getting-started/q/platform/android/
https://docs.aws.amazon.com/AWSAndroidSDK/latest/javadoc/com/amazonaws/mobileconnectors/iot/AWSIotMqttManager.html


---
<p align="center">
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
</p>
