---
title: 실습 1. 실습 환경 생성 및 IoT Core와의 통신
weight: 20
pre: "<b>1. </b>"
---

실습 진행이 어려우실 경우, [동영상(클릭)](/videos/IoT_Core_IMD_lab1.mp4)을 참조해 주시면 감사하겠습니다.

## 실습 환경 생성 및 IoT Core와의 통신

### CloudFormation template을 활용한 resource 생성

AWS CloudFormation을 사용하면 텍스트 파일 또는 프로그래밍 언어로 전체 인프라와 애플리케이션 리소스를 모델링할 수 있으며, 이와 같이 인프라를 코드화하면 인프라를 단순한 코드로 관리할 수 있습니다.\
본 실습에서도 미리 코드화된 CloudFormation을 활용하여 실습 환경을 만들게 됩니다.

아래 link를 클릭하여, workshop에서 활용할 기본적인 resource를 생성합니다. 이 외에 추가적인 리소스들은 여러분께서 Workshop을 통하여 생성하시게 됩니다.

[CloudFormation Link](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=iotcore-workshop101&templateURL=https://workshop-sehyul1.s3.ap-northeast-2.amazonaws.com/cfn/iotworkshop101.yml)를 클릭하여 다음과 같은 실습 환경을 생성하게 됩니다.

본 실습은 ***버지니아 북부(us-east-1)*** 리전을 기준으로 진행되며, 실습 환경도 동일한 리전에 생성됩니다.

'Create stack' 화면에서 ***Next***를 클릭하여, 다음 화면으로 진행합니다.

![picture1.png](//images/1/picture1.png)

'Specify stack details' 화면에서 ***Next***를 클릭하여 다음 화면으로 진행합니다.

![picture2.png](//images/1/picture2.png)

'Configure stack options' 화면에서도 ***Next***를 클릭하여, 다음 화면으로 진행합니다.

![picture3.png](//images/1/picture3.png)

마지막으로 Review화면에서 ***Create stack***을 클릭합니다.

![picture4.png](//images/1/picture4.png)

workshop에서 활용할 resource의 생성이 완료되는 데까지 수 분이 소요될 수 있습니다.

Resource 생성이 완료되면 Outputs 탭에서 Cloud9URL 항목을 찾아, 마우스 우클릭 및 Open Link in New Tab을 클릭하여 Cloud9 IDE로 이동합니다.

![picture5.png](//images/1/picture5.png)

잠시 후 Cloud9 IDE로 이동된 것을 보실 수 있습니다.

![picture6.png](//images/1/picture6.png)

설정 버튼(톱니바퀴 아이콘)을 클릭하여 ***Show Home in Favorites***를 체크합니다.

![picture7.png](//images/1/picture7.png)

<br/>
<br/>
<br/>

### IoT Core를 활용하여 Thing 만들기

이번 실습을 통하여 아래와 같이 Cloud9을 IoT Thing으로써 활용하여 IoT Core에 MQTT 메시지를 publish하는 실습을 진행합니다.

[IoT Core console](https://console.aws.amazon.com/iot/home?region=us-east-1#/connIntro)로 이동한 후, ***Connect > Get Started*** 메뉴에서 ***Get Started***를 클릭합니다.

![iotcore1.png](//images/1/iotcore1.png)

다음 화면에서 ***Get Started***를 클릭하여 진행합니다.

![iotcore2.png](//images/1/iotcore2.png)

Platform과 AWS IoT Device SDK에 사용하실 언어를 선택할 수 있습니다.
본 실습에서는 ***Linux/OSX*** 와 ***Python*** 을 선택합니다. 그리고, ***Next***를 클릭하여 다음 단계로 진행합니다.

![iotcore3.png](//images/1/iotcore3.png)

Thing(사물)의 이름을 지정하고 등록합니다. 예를 들어 ```MyThing```으로 지정하실 수 있습니다. ***Next step***을 클릭하여 다음 단계로 진행합니다.

![iotcore4.png](//images/1/iotcore4.png)

***Download connection kit for*** 아래의 버튼을 클릭하여, public key, private key, 인증서와 예제 script를 다운로드 받습니다. ***Next step***을 클릭하여 다음 단계로 진행합니다.

![iotcore5-1.png](//images/1/iotcore5-1.png)

***File > Upload Local Files...*** 메뉴를 이용하여, 다운로드 받은 ***connect_device_package.zip*** 파일을 Cloud9IDE에 업로드합니다.

![picture8.png](//images/1/picture8.png)

Cloud9 IDE에서 terminal을 열고 다음 Step대로 진행합니다.

```shell
unzip connect_device_package.zip
```

```shell
chmod +x start.sh
```

Cloud9 IDE에서 AWSIoTPythonSDK 설치 권한을 얻기 위하여 반드시 ***sudo***를 포함하여 아래 명령을 실행합니다.

```shell
sudo ./start.sh
```

***start.sh***를 실행시키시면 아래 화면과 같이 Cloud9 IDE에서 ***sdk/test/Python***이라는 Topic으로 MQTT 메시지를 publish하는 것을 보실 수 있습니다.

![picture9.png](//images/1/picture9.png)

그리고, ***Next***를 클릭하여 다음 단계로 진행합니다.

![iotcore6.png](//images/1/iotcore6.png)

***Connected successfully***화면을 보실 수 있습니다. ***Done***을 눌러서 설정을 종료합니다.

![iotcore8.png](//images/1/iotcore8.png)

[IoT Core > Test](https://us-east-1.console.aws.amazon.com/iot/#/test) 로 이동 후, ***sdk/test/Python***이라는 Topic을 subscribe합니다.

![iotcore9.png](//images/1/iotcore9.png)

그리고, Cloud9 IDE에서 publish하는 MQTT 메시지가 정상적으로 수신되는지 확인합니다.

![iotcore9.png](//images/1/iotcore10.png)

MQTT 메시지가 정상적으로 수신되는지 확인이 완료되면, Cloud9 IDE에서 ***Ctrl + C***를 입력하여 ***start.sh***를 종료합니다.
