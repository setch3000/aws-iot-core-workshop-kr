---
title: Rule engine 실습
weight: 30
pre: "<b>2. </b>"
---

## Rule engine을 활용한 IoT Database 구성

규칙(Rule)을 통해 디바이스가 AWS 서비스와 상호 작용할 수 있습니다.
본 실습에서는 AWS IoT Core의 rule engine을 활용하여, 사물(thing)으로 부터 수집된 데이터를 저장할 수 있는 Database를 구성합니다.

### DynamoDB table 생성

[DynamoDB 콘솔](https://console.aws.amazon.com/dynamodb/)로 이동합니다.

***Create Table***을 클릭합니다.

![ddb1.png](/images/2/ddb1.png)

아래와 같이 DynamoDB table을 생성합니다.

* Table name: ```IotDB``` 
* Partition key: ```SensorId``` (Data Type을 ***Number***로 지정해 줍니다.)
* Sort key: ```TimeStamp``` ((Data Type을 ***Number***로 지정해 줍니다.)

![ddb2.png](/images/2/ddb2.png)

### MQTT 토픽 변경과 policy 수정

Cloud9 IDE에서 다음 명령으로 새로운 script 파일을 만듭니다. Onboard a device를 통하여 다운로드 받은 start.sh 의 마지막 라인에 aws-iot-device-sdk-python/samples/basicPubSub/basicPubSub.py 을 실행시키기 위한 옵션들을 포함한 스크립트가 있으며, 이 부분만 재활용하기 위해서입니다.

```shell
tail -1 start.sh > run_basicpubsub.sh
chmod +x run_basicpubsub.sh
```

![cnine1.png](/images/2/cnine1.png)

이제 ***aws-iot-device-sdk-python/samples/basicPubSub/basicPubSub.py***을 수정하여 MQTT 토픽과 메시지를 변경할 것인니다. 파일 수정 권한에 문제가 없도록 하기 명령을 실행합니다.

```shell
sudo chown -R ec2-user aws-iot-device-sdk-python/
```

![cnine2.png](/images/2/cnine2.png)

좌즉 Environment탭에서 이제 ***aws-iot-device-sdk-python/samples/basicPubSub/basicPubSub.py***을 클릭하여 파일을 엽니다.

하기 두 개의 package를 import합니다(basicPubSub.py파일의 23~24 line).

```python
import random
from datetime import datetime
```

![cnine3.png](/images/2/cnine3.png)

하기와 같이 default Client Id, MQTT 토픽, mode를 아래와 같이 변경합니다(basicPubSub.py파일의 46~50 line 부근).

```python
parser.add_argument("-id", "--clientId", action="store", dest="clientId", default="MyThing",
                    help="Targeted client id")
parser.add_argument("-t", "--topic", action="store", dest="topic", default="$aws/rules/iotddb/iot/sensor", 
                    help="Targeted topic")
parser.add_argument("-m", "--mode", action="store", dest="mode", default="publish",
                    help="Operation modes: %s"%str(AllowedActions))
```

![cnine4.png](/images/2/cnine4.png)

MQTT 메시지 부분도 아래와 같이 변경합니다.(basicPubSub.py파일의 118 line 부근).

```python
        t = datetime.utcnow() 
        timeInSeconds = int((t-datetime(1970,1,1)).total_seconds())
        message = {}
        message['SensorId'] = 0
        message['SensorName'] = 'UltraSonic'
        message['TimeStamp'] = timeInSeconds
        message['Value'] = round(random.uniform(0.1, 9.9),2)
        messageJson = json.dumps(message)
```

![cnine5.png](/images/2/cnine5.png)

> 편집이 완료된 파일은 [여기에서 다운로드](https://sehyul.s3.ap-northeast-2.amazonaws.com/iot-core/basicPubSub.py) 받아보실 수도 있습니다. 하지만, 위 가이드와 같이 직접 Cloud9 IDE에서 직접 편집해 보시는 것을 추천드립니다.


***File > Save*** 메뉴를 선택하여 파일을 저장합니다.

![cnine6.png](/images/2/cnine6.png)

[IoT Core의 Secure > Policy 콘솔](https://console.aws.amazon.com/iot/#/policyhub)로 이동하고 앞서 생성한 MyThing과 연결된 MyThing-policy를 클릭합니다.

***Edit policy document***를 클릭하여 Policy를 편집합니다.

![policy1.png](/images/2/policy1.png)

14 Line과 26 Line 부근에서 ***topic_2***를 ```$aws/rules/iotddb/*```로 변경합니다. 두 군데를 변경해 주셔야 합니다.

![policy2.png](/images/2/policy2.png)

36 Line 부근에서 ***basicPubSub*** 를 ```MyThing```로 변경합니다.
그리고, ***Save as New version***을 클릭하여 저장합니다.

![policy3.png](/images/2/policy3.png)

수정된 policy는 아래와 같은 형식이 됩니다.

![policy4.png](/images/2/policy4.png)

### Rule 생성

[AWS IoT Core의 Act > Rules 콘솔](https://console.aws.amazon.com/iot/#/rulehub)로 이동하고 ***Create a rule***을 클릭합니다.

![ddb_rule1.png](/images/2/ddb_rule1.png)

생성할 rule의 이름을 입력합니다. 예를 들어 ```iotddb```와 같이 입력할 수 있습니다.

Rule query statement에는 아래와 같이 입력합니다.

```sql
SELECT * FROM 'iot/sensor'
```

![ddb_rule2.png](/images/2/ddb_rule2.png)

Set one or more actions에서 ***Add action*** 을 클릭하여 action을 추가합니다.

![rule3.png](/images/2/rule3.png)

Select an action에서 ***Insert a message into a DynamoDB Table***을 선택하고 ***Configure Action*** 버튼을 클릭하여 다음 단계로 진행합니다.

![ddb_rule3.png](/images/2/ddb_rule3.png)

Table name에서 앞서 생성한 ```IotDB```을 찾아서 입력합니다.
그리고, 하기와 같이 입력합니다.

* Partition key value: ```${SensorId}```
* Sort key value: ```${TimeStamp}```

![ddb_rule4.png](/images/2/ddb_rule4.png)


IoT Core서비스가 DynamoDB와 같은 다른 서비스에 접근하기 위한 permmision을 부여하기 위하여 IAM Role을 생성해야 합니다. ***Create Role*** 버튼을 클릭합니다.

![ddb_rule5.png](/images/2/ddb_rule5.png)

Name에는 예를 들어 ```iotddb_role```이라고 입력할 수 있습니다.

![ddb_rule6.png](/images/2/ddb_rule6.png)

IAM role이 생성되면 ***Add action***을 클릭하여 rule action 추가를 완료합니다.

![ddb_rule7.png](/images/2/ddb_rule7.png)

***Create rule***을 클릭하여 rule 생성을 완료합니다.

![ddb_rule8.png](/images/2/ddb_rule8.png)

### 테스트

Cloud9 IDE에서 앞서 생성한 ***run_basicpubsub.sh*** 실행합니다.

```shell
  ./run_basicpubsub.sh
```

그리고, 정상적으로 IoT Core에 MQTT 메시지를 publish하는지 확인합니다.

![test1.png](/images/2/test1.png)

[DynamoDB 콘솔](https://console.aws.amazon.com/dynamodb/)로 이동한 후, 앞서 생성한 ***IotDB*** 테이블을 선택합니다.
***Items 탭 > Start search*** 를 클릭하여 DynamoDB에 저장된 데이터들을 스캔(scan)합니다.

![ddb_rule8.png](/images/2/ddb_rule9.png)

수고하셨습니다.
