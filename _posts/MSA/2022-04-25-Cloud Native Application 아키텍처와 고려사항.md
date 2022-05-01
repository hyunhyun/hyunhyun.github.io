---
title: Cloud Native Application 아키텍처와 고려사항
date : 2022-04-25
categories : [MSA]
tags: [MSA]
---

### Cloud Native Application - CI/CD
* 지속적인 통합,  CI(Continuous Integration)
    * ex) Jenkins, Team CI, Travis CI
    * 빌드, 테스트 까지 수행
* 지속적 배포 CD
    * Continuous Delivery : 수동반영이 필요한 경우 continuous delivery 이후 , 수동반영
    * Continuous Deployment : 자동반영 까지
* 배포전략
    * 카나리 배포
        * 일부 사용자들에게만 신버전을 오픈하고 이후 점진적으로 늘려가는 방식
    * 블루그린 배포 방식 : 구버전(그린), 신버전(블루)
        * 신버전 배포 후 일제히 신버전으로 전환하는 방식

### 컨테이너 가상화
하드웨어 가상화, 서버 가상화에 비해서 적은 리소스로 가상화 서비스를 제공
* 하드웨어 가상화 : 물리적인 하드웨어(호스트 시스템)을 쪼개서 사용 host 운영 체제에 많은 부하를 줌, 시스템 확장에 한계
* 컨테이너 가상화 : 운영 체제 위에 container runtime(컨테이너 가상화를 기동하기 위한 서비스) 위에 서비스를 올려서 사용
* container runtime 에서 공통적인 라이브러리나 리소스를 공유해서 사용
* 각자 필요한 부분에 대해서만 독립적인 영역에다가 실행할수 있는 구조제
* 컨테이너 가상화 위에 운영되는 서비스는 가볍고 빠르게 운영 가능

### 12factors
Cloud Native 어플리케이션 개발 및 운영시 고려해야될 항목
1) code base : 자체 리파지토리에 저장된 각 마이크로서비스에 대한 단일 코드 base
   버전 관리를 위한 목적, 코드 배포를 한곳에서 함, 코드의 통일적인 관리
2) dependency isolation
3) 시스템 코드 외부에서 작업들을 제어
4) 보조 서비스 : 캐시,메시징 서비스, 브로커를 이용해서 마이크로 서비스가 가져야될 기능을 추가적으로 지원하는것을 활용
   5)build, release, run 분리 : 각 기능 분리, cicd 이용해서 자동화된 파이프라인 구축하는것이 바람직, 각 단계 실패 시 rollback 기능 필요
6) process: 하나의 마이크로서비스는 분리되어서 독립적으로 운용될 수 있어야함
7) port binding : 자체 포트에서 노출되는 인터페이스와 함께 포함되어있는 기능이 있어야함, 다른 마이크로 서비스와 격리가 가능하도록
   8)동시성 : 동일 서비스가 복사 되어 운영됨으로 부하 분산, 여러 인스턴스가 있기 때문에 동시성이 유지되어야함
9) disposabilitly : 서비스 인스턴스 자체가 삭제가 가능해야함, 확장성을 높이고 정상적으로 종료가 가능해야함
10) development 단계와 production 단계의 분리
11) log : log를 이벤트 stream 으로 처리되어야함, 별도의 모니터링, 로그 관리 도구 사용 가능
12) 적절한 관리도구 : 데이터 정리, 데이터 분석, 알람

***
* 참고 : (인프런)Spring Cloud로 개발하는 마이크로서비스 애플리케이션(MSA) - Dowon Lee
* [링크](https://inflearn.com/course/스프링-클라우드-마이크로서비스/dashboard)
