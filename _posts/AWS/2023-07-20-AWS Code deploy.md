---
title: AWS Code deploy
date : 2023-07-20
categories : [AWS]
tags: [AWS]
---
# AWS code deploy

code deploy를 생성하여 배포하는 과정을 만들어보겠다.

code deploy에 들어가서 code deploy application을 생성한다.

iam role 로 아래와 같이 AWSCodeDeployRole 권한이 있는 role을 생성한다.

![스크린샷 2023-07-20 오후 5 27 47](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/09520207-d688-41e7-9616-8578d87baca5)

아래와 같이 배포 그룹을 생성한다. 배포에 추가할 인스턴스 조합으로 EC2 인스턴스를 선택한다. 그리고 아래에서 적절한 생선한 ec2 중 적절한 ec2를 선택한다.

![스크린샷 2023-07-20 오후 5 30 18](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/f71f33d8-2e1e-44cd-a974-4c89e43c0917)

로드밸런스는 선택하지 않고 단일 ec2에만 적용할 것이니 로드 밸런싱 활성화는 해제해 준다.
![스크린샷 2023-07-20 오후 5 32 17](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/1d2cdf9d-2d4a-47f3-88e8-9cfe4b98ab42)

## 소스 코드에 appspec\.yml, 배포 script 추가

루트 폴더 바로 아래에 appspec\.yml 도 추가한다

```yml
#appspec.yml
version: 0.0 # version 필수 값, 0.0이 고정
os: linux # ec2에 배포하는 경우 필수 값, amazon linux -> linux

files:
- source:  /
destination: /home/ec2-user/aws  # Artifact가 unzip된 결과가 생성될 디렉토리명, 배포할 위치
overwrite: yes

hooks:
ApplicationStart:
- location: /deploy.sh    # ApplicationStart 샘명 주기에서 deploy.sh 실행
```
루트 폴더 아래에 \'scripts\/deploy\.sh\' 파일을 생성한다

```bash
#!/bin/bash
#BUILD_JAR=$(ls /home/ec2-user/aws/build/libs/*.jar)     # jar가 위치하는 곳
BUILD_JAR=$(ls /home/ec2-user/aws/*.jar)     # jar가 위치하는 곳
JAR_NAME=$(basename $BUILD_JAR)
echo "> build 파일명: $JAR_NAME" >> /home/ec2-user/deploy.log

echo "> build 파일 복사" >> /home/ec2-user/deploy.log
DEPLOY_PATH=/home/ec2-user/
cp $BUILD_JAR $DEPLOY_PATH

echo "> 현재 실행중인 애플리케이션 pid 확인" >> /home/ec2-user/deploy.log
CURRENT_PID=$(pgrep -f $JAR_NAME)

if [ -z $CURRENT_PID ] #-z 문자열이 길이가 0이면 참
then
  echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다." >> /home/ec2-user/deploy.log
else
  echo "> kill -15 $CURRENT_PID"
  kill -15 $CURRENT_PID           #15 : SIGTERM:  소프트웨어 종료 시그널
  sleep 5
fi

DEPLOY_JAR=$DEPLOY_PATH$JAR_NAME
echo "> DEPLOY_JAR 배포"    >> /home/ec2-user/deploy.log
nohup java -jar $DEPLOY_JAR >> /home/ec2-user/deploy.log 2>/home/ec2-user/deploy_err.log &
```

참고블로그[https://twofootdog.tistory.com/38]

위의 블로그를 보고 스크립트를 작성했는데 **BUILD_JAR\=\$\(ls /home/ec2\-user/aws/build\libs/\*\.jar\)** 라고 되어있는 위치가 내 경우에는 **buildspec\.yml** 에서 설정한 **discard\-paths\: yes** 때문인지 위치가 루트 바로 아래에 위치하게 되었어서 위치가 달라서

```sh
script does not exist at specified location
```
라는 오류가 떴어서 위치 조정을 해주었다

code deploy 관련해서 ec2에 생성되는 로그는

/var/log/aws/codedeploy\-agent 해당 위치에서 로그를 확인 가능했다

그리고 EC2에 codedeploy 가 가능하게 하려면
배포하려는 EC2 에 AWSCodeDeployRole을 추가 해 주고 codedeploy agent 설치도 해주어야한다

## EC2 AWSCodeDeployRole 추가
![screenshot](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/63d238d4-8d96-4f75-80a2-c2f8558d9f22)

위와 같이 AWSCodeDeployRole 권한이 있는 role을 생성해주고 해당 role을 ec2에 IAM Role로 설정해준다\.<br>
아래 권한의 SSM은 EC2에 sessionManager를 통해서 접속이 가능하도록 하는 권한이다\.

## EC2 CodeDeploy Agent 설치

```sh
wget https://aws-codedeploy-ap-northeast-2.s3.ap-northeast-2.amazonaws.com/latest/install
```
실행 권한 추가
```sh
chmod +x ./install
```
```sh
sudo ./install auto
```

```sh
# codedeploy-agent 상태 확인
sudo service codedeploy-agent status

# codedeploy-agent 서비스 시작
sudo service codedeploy-agent start
```
status 로 codedeploy agent이 service up 이 되어 있는지 확인한다