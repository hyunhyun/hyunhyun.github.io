---
title: AWS Code pipeline
date : 2023-07-20
categories : [AWS]
tags: [AWS]
---
# AWS code pipeline cicd 구축하기

이전 게시글에서 aws code commit, code build, code deploy를 만들었다.
이들을 연결하여 pipeline을 생성하여 cicd를 구축해보자.


소스 공급자로 이전에 code commit에 저장된 리파지토리를 선택한다.
![스크린샷 2023-07-20 오후 6 07 08](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/9923db8b-bd13-4be2-9c9b-46fef657d3f3)


빌드 스테이지에서는 이전에 생성한 code build project를 선택한다.
![스크린샷 2023-07-20 오후 6 08 10](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/d494f0cc-b7e6-4db8-ba7c-c17ded0896a6)


배포 스테이지에서는 이전에 생성한 code deploy application을 선택한다.
![스크린샷 2023-07-20 오후 6 09 34](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/e13f7e22-880b-4653-8caa-2dad78c72b15)

이후 code commit 해당 리파지토리에 push가 일어나면 자동으로 source 단계부터 차례로 이전 단계가 성공적으로 수행되면 그다음 단계가 수행된다.

![스크린샷 2023-07-20 오후 6 10 47](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/391514c6-d82b-4c98-9496-4266d42c36d4)


```java
@RestController
public class TestController {
    @GetMapping("/test")
    public String testGet(){
        return "success";
    }
}
```
배포한 소스에 위와 같은 코드를 넣어 정상 배포 되었는지 확인해 볼것이다.
tomcat의 기본 포트인 8080의 접속을 가능하게 하기 위해서 EC2의 보안그룹에 8080포트에 inbound 규칙이 있는지 확인한다.

<img width="696" alt="스크린샷 2023-07-20 오후 6 24 32" src="https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/c9bc6494-a43d-4fa8-86bd-40d824a27535">

위와 같이 정상 배포 후 접속까지 되는것을 확인해 볼 수 있다.