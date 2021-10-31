---
title: Thing Shadow 실습
weight: 40
pre: "<b>3. </b>"
---

## Thing Shadow를 위한 policy 수정

[IoT Core의 Secure > Policy 콘솔](https://console.aws.amazon.com/iot/#/policyhub)로 이동하고 앞서 생성한 MyThing과 연결된 MyThing-policy를 클릭합니다.

***Edit policy document***를 클릭하여 policy를 편집합니다.

13 Line과 25 Line 부근에서 ***topic_1***을 ```$aws/things/MyThing/shadow/*```로 변경합니다. 두 군데를 변경해 주셔야 합니다.
그리고, ***Save as New version***을 클릭하여 저장합니다.

![policy1.png](/images/3/policy1.png)

수정된 policy는 아래와 같은 형식이 됩니다.
![policy2.png](/images/3/policy2.png)


## Thing Shadow를 위한 sample application 수정 (1)

Cloud9 IDE terminal에서 pip로 AWSIoTPythonSDK를 설치합니다.
아래와 같은 명령을 사용할 수 있습니다.

```python
sudo python36 -m pip install AWSIoTPythonSDK  
```

Cloud9 IDE에서 ***aws-iot-device-sdk-python/samples/basicShadow/basicShadowUpdater.py*** 파일을 엵고 thingName과 clientId를 변경하여 줍니다 (70~71 Line).

```python
parser.add_argument("-n", "--thingName", action="store", dest="thingName", default="MyThing", help="Targeted thing name")
parser.add_argument("-id", "--clientId", action="store", dest="clientId", default="MyThing", help="Targeted client id")
```

![cnine_lab2_1.png](/images/3/cnine_lab2_1.png)

***aws-iot-device-sdk-python/samples/basicShadow/basicShadowUpdater.py*** 파일에서 ***desired*** 객체의 이름을 ***reported*** 로 변경하기 위하여 하기와 같이 수정합니다. Cloud9 IDE의 editor에서 ***Ctrl + F***로 검색하시면 쉽게 찾으실 수 있습니다.

46 라인 부근에서 ***desired***를 ***reported*** 로 변경해 줍니다.
![cnine_lab2_4.png](/images/3/cnine_lab2_4.png)

134 라인 부근에서 ***desired***를 ***reported*** 로 변경해 줍니다.
![cnine_lab2_5.png](/images/3/cnine_lab2_5.png)

132 라인 부근에서 ***while True:*** 를 'while True and loopCount < 50:'로 변경해 줍니다.
![cnine_lab2_6.png](/images/3/cnine_lab2_6.png)


> 편집이 완료된 파일은 [여기에서 다운로드](https://sehyul.s3.ap-northeast-2.amazonaws.com/iot-core/basicShadowUpdater.py) 받아보실 수도 있습니다. 하지만, 위 가이드와 같이 직접 Cloud9 IDE에서 직접 편집해 보시는 것을 추천드립니다.

## 테스트 (1)

소스 코드 편집이 완료되면, 하기와 같이 앞서 사용한 ***run_basicpubsub.sh*** 파일을 복사하여 새로운 파일을 생성합니다.

```shell
cp run_basicpubsub.sh run_shadow.sh
```

Cloud9 IDE에서 새로 생성한 ***run_shadow.sh*** 파일을 열고 파일 경로를 ```aws-iot-device-sdk-python/samples/basicShadow/basicShadowUpdater.py```로 변경하여 줍니다.

![cnine_lab2_2.png](/images/3/cnine_lab2_2.png)

***run_shadow.sh*** 을 실행합니다.

```shell
./run_shadow.sh
```

shadow가 지속적으로 업데이트되는 것을 확인합니다.

![cnine_lab2_3.png](/images/3/cnine_lab2_3.png)

[IoT Core의 Manage > Things 콘솔](https://console.aws.amazon.com/iot/#/thinghub)로 이동 후, 앞서 생성한 ***MyThing***을 클릭합니다.

Classic Shadow를 클릭합니다.

![policy0.png](/images/3/shadow0.png)

Shadow state와 metaddata가 지속적으로 변경(update)되는 것을 확인할 수 있습니다.

![shadow1.png](/images/3/shadow1-1.png)

<!-- 확인이 완료되면, Cloud9 IDE에서 ***Ctrl + C***를 입력하여 ***run_shadow.sh***를 종료합니다. -->


## Thing Shadow를 위한 sample application 수정 (2)


Cloud9 IDE에서 ***aws-iot-device-sdk-python/samples/basicShadow/basicShadowDeltaListener.py*** 파일을 엵고 thingName과 clientId를 변경하여 줍니다 (57~58 Line).

```python
parser.add_argument("-n", "--thingName", action="store", dest="thingName", default="MyThing", help="Targeted thing name")
parser.add_argument("-id", "--clientId", action="store", dest="clientId", default="MyThing", help="Targeted client id")
```

![shadow-listener1.png](/images/3/shadow-listener1.png)


## 테스트 (2)

소스 코드 편집이 완료되면, 하기와 같이 앞서 사용한 ***run_shadow.sh*** 파일을 복사하여 새로운 파일을 생성합니다.

```shell
cp run_shadow.sh run_shadow_listener.sh
```

Cloud9 IDE에서 새로 생성한 ***run_shadow_listener.sh*** 파일을 열고 파일 경로를 ```aws-iot-device-sdk-python/samples/basicShadow/basicShadowDeltaListener.py```로 변경하여 줍니다.

***run_shadow_listener.sh*** 을 실행합니다.

```shell
./run_shadow_listener.sh
```
![shadow-listener2.png](/images/3/shadow-listener2.png)


[IoT Core > Test](https://us-east-1.console.aws.amazon.com/iot/#/test) 로 이동 후, '$aws/things/MyThing/shadow/update/+'이라는 Topic을 subscribe합니다.

![shadow-listener3.png](/images/3/shadow-listener3.png)

이어서 'Publish to a topic' 탭을 클릭하고 ***$aws/things/MyThing/shadow/update***이라는 topic을 publish 합니다.


Message payload는 아래와 같이 입력하여 줍니다.

```json
{
  "state": {
    "desired": {
      "property": 0
    }
  },
  "version": 1
}
```

![shadow-listener4.png](/images/3/shadow-listener4.png)

Shadow의 버전이 일치하지 않기 때문에 아래와 같이 ***$aws/things/MyThing/shadow/update/rejected*** 토픽으로 "Version conflict" 응답이 수신되는 것을 보실 수 있습니다.

```json
{
  "code": 409,
  "message": "Version conflict"
}
```

![shadow-listener5.png](/images/3/shadow-listener5.png)

다시 한 번 'Publish to a topic' 탭을 클릭하고 ***$aws/things/MyThing/shadow/update***이라는 topic을 publish 합니다.

Message payload는 아래와 같이 입력하여 줍니다.

```json
{
  "state": {
    "desired": {
      "property": 0
    }
  }
}
```

{{% notice info %}}
위 payload와 같이 "version"을 쉐도우에 포함시키지 않아도 되지만, 실제 활용하실 때에는 쉐도우의 최종 버전과 동일하게 맞춰 주시는 것이 좋습니다.
예를 들면 쉐도우의 최종 버전이 50인 경우는 다음과 같이 payload를 사용해 주시면 됩니다.
{
  "state": {
    "desired": {
      "property": 0
    }
  },
  "version": 50
}

{{% /notice %}}

![shadow-listener6.png](/images/3/shadow-listener6.png)


이번에는 정상적으로 쉐도우가 처리되었기 때문에 Shadow의 버전이 일치하지 않기 때문에 아래와 같이 ***$aws/things/MyThing/shadow/update/accepted*** 토픽과 ***$aws/things/MyThing/shadow/update/documents*** 토픽으로 메시지가 수신되는 것을 확인하실 수 있습니다. 그리고,"desired"와 "reported"의 상태가 서로 다르므로 ***$aws/things/MyThing/shadow/update/delta*** 토픽으로도 메시지가 발생되는 것을 보실 수 있습니다.

![shadow-listener7.png](/images/3/shadow-listener7.png)


Cloud9 IDE의 터미널을 확인해 보시면, DELTA가 수신된 것을 확인하실 수 있습니다.

![shadow-listener8.png](/images/3/shadow-listener8.png)

확인이 완료되면, Cloud9 IDE에서 ***Ctrl + C***를 입력하여 ***run_shadow_listener.sh***를 종료합니다.

수고하셨습니다.

---
<p align="center">
© 2020 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
</p>
