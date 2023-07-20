---
title: Code Build 사용하기
date : 2023-07-20
categories : [AWS]
tags: [AWS]
---

# AWS code pipeline 활용하기 위해 code build 빌드 프로젝트 생성

aws code pipeline을 활용하여 cicd를 구축하기 위한 단계로
- 소스 리파지토리는 aws code commit 사용
- build 단계에서는 code build를 사용
- deploy 단계에서는 code deploy 사용

CodeBuild에서 빌드 프로젝트를 생성해준다
빌드할 소스의 위치를 전에 생성한 git commit으로 선택후 repository\, branch 을 선택해준다
![screenshot](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/4fbe7201-5bb5-4ca1-95fc-ba4f9e514c46)

환경이 운영체제는 해당 사항에 맞게 선택해주고
corretto11을 선택해 주었다\.
기존 role이 없다면 새 서비스 역할을 선택해 주면 codebuild에 맞는 역할이 생성된다
![screenshot](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/b12aeb4d-bb15-460a-bbd3-559c3481fc79)

아래와 같이 codebuild에서 buildspec\.yml 파일이 있어야 한다
![screenshot](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/69d69132-212c-4423-8d39-08bb26587ed0)

프로젝트 소스 루트 바로 아래에 buildspec\.yml 파일을 생성한다
```yml
#buildspec.yml
version: 0.2

phases:
install:
runtime-versions:
java: corretto11

build:
commands:
- echo Build Starting
- chmod +x ./gradlew
- ./gradlew bootJar
post_build:
commands:
- pwd
artifacts: # 빌드 결과 파일
files: # build 후 패키징 할 때 jar 파일에 scripts 아래 파일들 추가
- build/libs/*.jar
- appspec.yml
- scripts/**
discard-paths: yes # 빌드 결과 파일이 업로드 될때 path 명은 무시되고 파일명으로만 업로드 됨

cache:
paths:
- '/root/.gradle/caches/**/*'
```

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

위의 블로그를 보고 스크립트를 작성했는데 **BUILD_JAR\=\$\(ls \/home\/ec2\-user\/aws\/build\/libs\/\*\.jar\)** 라고 되어있는 위치가 내 경우에는 **buildspec\.yml** 에서 설정한 **discard\-paths\: yes** 때문인지 위치가 루트 바로 아래에 위치하게 되었어서 위치가 달라서

```sh
script does not exist at specified location
```
라는 오류가 떴어서 위치 조정을 해주었다

code deploy 관련해서 ec2에 생성되는 로그는

\/var\/log\/aws\/codedeploy\-agent 해당 위치에서 로그를 확인 가능했다

그리고 EC2에 codedeploy 가 가능하게 하려면
배포하려는 EC2 에 AWSCodeDeployRole을 추가 해 주고 codedeploy agent 설치도 해주어야한다

### AWSCodeDeployRole 추가
![screenshot](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/63d238d4-8d96-4f75-80a2-c2f8558d9f22)

위와 같이 AWSCodeDeployRole 권한이 있는 role을 생성해주고 해당 role을 ec2에 IAM Role로 설정해준다\.<br>
아래 권한의 SSM은 EC2에 sessionManager를 통해서 접속이 가능하도록 하는 권한이다\.

### CodeDeploy Agent 설치

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
