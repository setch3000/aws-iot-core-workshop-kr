---
title: 실습 종료 후 리소스 정리
weight: 90
pre: "<b></b>"
---

## 실습 종료 후 리소스 정리

실습이 종료되면, 실습에 사용된 리소스들을 모두 삭제해 주셔야 불필요한 과금을 피하실 수 있습니다.

### DynamoDB table 삭제

[DynamoDB 콘솔](https://console.aws.amazon.com/dynamodb/)로 이동합니다.
좌측 메뉴에서 ***Tables***를 클릭하고 실습 과정 중에 생성한 ***IotDB***를 클릭합니다.
그리고 ***Delete table***을 클릭하여 테이블을 삭제합니다.

![delete1.png](images/cleanup/delete1.png)

### Thing 삭제

[IoT Core의 Secure > Policy 콘솔](https://console.aws.amazon.com/iot/#/policyhub)로 이동하고 앞서 생성한 MyThing과 연결된 MyThing-policy를 클릭합니다.

좌측 메뉴에서 ***Versions***를 클릭하고 최신(Active) 버전 외에는 모두 삭제해 줍니다.
***...*** 메뉴를 클릭하고 ***Delete***를 클릭하여 삭제할 수 있습니다.

![delete2.png](images/cleanup/delete2.png)

그런 다음 우측 상단의 ***Actions > Delete***를 클릭하여 policy를 삭제합니다.

![delete3.png](images/cleanup/delete3.png)

[IoT Core의 Secure > Certificates 콘솔](https://console.aws.amazon.com/iot/#/certificatehub)로 이동하고 ***delete***를 클릭하여 실습에 사용했던 인증서를 삭제합니다.

![delete4.png](images/cleanup/delete4.png)

[IoT Core의 Manage > Things 콘솔](https://console.aws.amazon.com/iot/#/thinghub)로 이동하고 ***delete***를 클릭하여 실습에 사용했던 사물(thing)을 삭제합니다.

![delete5.png](images/cleanup/delete5.png)

### IAM Role 삭제

[IAM의 Role 콘솔](https://console.aws.amazon.com/iam/#/roles)로 이동하고 실습에 사용했던 IAM Role을 검색하여 찾은 후, ***delete***를 클릭하여 삭제합니다. 예를 들어 ***iotddb_role***과 같은 이름으로 실습 과정에서 IAM Role을 생성하셨다면 이것을 찾아서 삭제합니다.

![delete6.png](images/cleanup/delete6.png)

### CloudFormation 스택 삭제

[CloudFormation 콘솔](https://console.aws.amazon.com/cloudformation)로 이동하고 실습 과정 중에 생성한 CloudFormation stack을 선택한 후 ***delete***를 삭제합니다. ***aws-cloud9-test-poc-Cloud9-IDE-고유번호*** 형식의 스택은 실습에서 생성하신 Stack을 삭제하시면 함께 삭제됩니다.

![delete7.png](images/cleanup/delete7.png)

수고하셨습니다.\
이제 모든 리소스 삭제를 완료하셨습니다.

---
<p align="center">
© 2021 Amazon Web Services, Inc. 또는 자회사, All rights reserved.
</p>
