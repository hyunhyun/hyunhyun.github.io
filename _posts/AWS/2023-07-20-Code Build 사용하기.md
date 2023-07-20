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
빌드할 소스의 위치를 전에 생성한 git commit으로 선택후 repository, branch 을 선택해준다
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


