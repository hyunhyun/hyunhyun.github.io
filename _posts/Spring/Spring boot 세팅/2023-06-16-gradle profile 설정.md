---
title: gradle profile 다르게 설정
date : 2023-06-16
categories : [Spring, Spring boot 세팅]
tags: [API, Spring boot 세팅, Swagger]
---
# gradle profile 별로 다르게 설정하기

### gradle이 아닌 gradelw -gradle wrapper를 사용하는 이유 
gradlew를 이용하여 빌드하면 로컬환경 java와 gradle버전과 상관없이 새로운 프로젝트를 빌드할 수 있다.

### gradle 설정
profile의 기본값 dev로 설정
<br>
```gradle
ext.profile = (!project.hasProperty('profile')) || !profile) ? 'dev' : profile
```
```gradle
//리소스 디렉토리 추가
sourceSets{
  main{
    resources{
      srcDirs "src/main/resources-${profile}"
    }
  }
}
```
### yml 설정
```yml
// /main/resources-prod/application-prod.yml 파일

common: prod-common
test: prod-test
```

#### 아래를 순서대로 적용 한다
build 폴더 삭제됨
```
./gradlew clean
```

build resources 아래에 해당 profile yml만 생성됨
 
```
./gradlew clean bootJar -Pprofile=prod  
```


 ```
 java -jar ./build/libs/jar명.jar --spring.profiles.active=prod
```
  


#### @Value로 설정파일 값을 잘 가져오나 확인해보자
```java
@SpringBootApplication
public class LogApplication {

    @Value("${common}")
    private String common;

    @Value("${test}")
    private String test;

    public static void main(String[] args) {
        SpringApplication.run(LogApplication.class, args);
    }

    @PostConstruct
    private void start() {
        System.out.println("common = " + common);
        System.out.println("test = " + test);
    }
}
```

  [참고블로그](https://velog.io/@haerong22/Spring-%EB%B0%B0%ED%8F%AC-%ED%99%98%EA%B2%BD-%EB%B3%84%EB%A1%9C-%EC%84%A4%EC%A0%95%ED%8C%8C%EC%9D%BC-%EB%B6%84%EB%A6%AC%ED%95%98%EA%B8%B0feat.-gradle)