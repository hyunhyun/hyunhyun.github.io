---
title: Code Commit Repositorty 사용하기
date : 2023-07-18
categories : [AWS]
tags: [AWS]
---
# Repository로 AWS Code Commit 사용하기
![스크린샷 2023-07-18 오후 9 58 40](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/30e2253a-3690-4dbb-b927-4ff3c4ddc86f)

code commit 에서 리파지토리를 새로 생성해준다.

iam 메뉴에 가서 사용자를 새로 생성한다.

![스크린샷 2023-07-18 오후 10 11 09](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/c03be756-f813-433a-afdc-036111692323)

사용자의 권한에 AWSCodeCommitFullAccess권한을 추가해준다.

사용자의 보안자격 증명 탭에 들어가면 아래에
AWS CodeCommit에 대한 HTTPS Git 자격 증명에 아래와 같이 자격 증명을 하나 생성한다. 이때 암호는 따로 저장해 둔다.
![스크린샷 2023-07-18 오후 10 14 05](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/878f990a-4ed3-4da4-b74f-707e4c345b3c)

그리고 기존의 git 사용법 대로 아래 명령어 순서대로 수행하여준다.

```bash
git init

git add .

git commit -m "commit message"

git remote add origin https://git-codecommit.ap-northeast-2.amazonaws.com/v1/repos/hyun-repo

git push -u origin master

```

여기까지 하면 username, password 를 입력하라고 나오는데 이때 
전에 만든 https git 자격 증명의 사용자 이름과 생성 시 자동으로 생성된 암호를 입략한다.
그럼 github등과 동일하게 aws code commit에 소스가 업로드 된 걸 확인 할 수 있다.

![스크린샷 2023-07-18 오후 10 29 55](https://github.com/hyunhyun/hyunhyun.github.io/assets/18597515/8c9a67ac-2906-40fd-b82c-fae1c700f885)

