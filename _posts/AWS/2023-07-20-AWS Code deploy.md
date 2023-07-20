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