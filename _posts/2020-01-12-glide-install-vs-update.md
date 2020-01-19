---
title:  "glide 기본 사용법과 yaml, lock파일 이해하기"
excerpt: "golang의 package manager인 glide의 사용법을 npm과 비교하며 알아보고 glide.yaml, glide.lock 파일을 알아보자"

categories:
  - LearnFromExample
tags:
  - golang
  - go
  - package manager
  - glide
  - glide.yaml
  - glide.lock
last_modified_at: 2020-01-12 18:00:00
---
javascript 개발자라면 굉장히 익숙하고 자주 쓸 수 밖에 없는 명령어가 하나 있습니다.  
바로 **npm**이라는 javascript의 package manager입니다.  

새로운 트렌드 언어답게 golang에도 dep나 glide 등의 package manager가 있는데, 여기서 소개할 것은 glide입니다.  
package manager를 안쓰면 엄청난 고생을 할 수 있으니 한 가지는 꼭 사용합시다.  
예를 들어 다른 사람이 올린 go project 파일을 받아서 실행해보려는데 엄청난 에러에 휩싸일 수 있습니다.  
```bash
go get -u -v ./...
```
이 명령어를 이용하면 프로젝트에 쓰인 모든 package를 한 번에 받을 수 있긴 합니다.  

설치 방법은 자신의 운영 환경에 따라 다릅니다.  
[이곳](https://glide.readthedocs.io/en/latest/) 하단에서 확인할 수 있으며 구글링해도 매우 많습니다.  

쉬운 이해를 이해 간단히 npm과 비교하며 설명하겠습니다.


## 사용 방법
```bash
$ glide init
```
1. javascript 프로젝트 내에 package.json 파일이 없다면 무엇을 하시나요? 아마 **npm init**부터 할 것입니다.  
go 역시 프로젝트 내에 glide.yaml 파일이 없다면 이 명령어 부터 시작하면 됩니다. glide.yaml 파일을 생성해줍니다.  
프로젝트 진행 도중에 이를 실행하면 해당 프로젝트 파일들을 모두 읽으며 import되어 있는 package들, package에 쓰이는 또 다른 package들을 모두 확인하여 glide.yaml 파일을 만들어줍니다.  

```bash
$ glide get {package 경로}
```
2. javascript 프로젝트에 **npm install {package 이름}**을 이용해서 추가 package를 설치했었죠?  
go 역시 필요한 package를 설치할 때 이 명령어를 사용합니다.  
install이 아니라 get이라는 차이가 있습니다.  
실행이 후 glide.lock 파일이 없었다면 새로 생기고 glide.yaml 파일과 함께 설치한 package가 등록될 것입니다.  
그리고 vendor라는 폴더가 생겼을 텐데 이 폴더는 javascript 프로젝트의 **node_modules**와 같은 폴더입니다.  

```bash
$ glide install
```
3. git에서 javascript 프로젝트를 새로 받으면 **npm install**을 해줬었습니다.  
2번에서 get을 사용한 이유가 여기서 install을 쓰기 때문입니다.  
명령어를 다르게 사용함으로써 덜 헷갈리고 기억할 수 있는 것 같습니다.  
이 명령어도 2번의 명령어처럼 glide.lock 파일, vendor라는 폴더가 새로 생깁니다.  

```bash
$ glide remove {package 경로}
  or
$ glide rm {package 경로}
```
4. 잘못 설치했던 javascript package는 **npm uninstall {package 이름}**으로 지웠었습니다.  
go는 필요 없어진 package를 지울 때 이 명령어를 사용합니다.
실행이 후 glide.yaml, glide.lock파일이 갱신되고 vendor 폴더에서 해당 package가 삭제됩니다.


## glide.yaml과 glide.lock 파일의 version
아래 내용이 들어있는 glide.yaml 파일이 있습니다.
```
- package: google.golang.org/grpc
  version: v1.23.1
- package: github.com/golang/protobuf
  version: v1.0.0
  subpackages:
  - proto
```
이 상태로 **glide install**을 실행하면 이런 glide.lock 파일이 생성됩니다.
```
- name: google.golang.org/grpc
  version: f95447b3d586dc2a1f350ea5294ad32eb9f2b827
- name: github.com/golang/protobuf
  version: 925541529c1fa6821df4e44ce2723319eb2be768
  subpackages:
  - proto
```

하지만 glide.yaml 파일을 이렇게 수정하고 **glide install**하면 어떻게 될까요?
```
- package: google.golang.org/grpc
- package: github.com/golang/protobuf
  version: v1.0.0
  subpackages:
  - proto
```
바로 이런 glide.lock 파일이 생성됩니다.
```
- name: google.golang.org/grpc
  version: 3311b9ea68a8c6c1554506af68fcbd9fe3e1c74f
- name: github.com/golang/protobuf
  version: 925541529c1fa6821df4e44ce2723319eb2be768
  subpackages:
  - proto
```

차이가 느껴지나요?  

glide.yaml 파일에 version 값이 있다면 해당 버전에 맞는 package를 설치해주고,  
version 값이 없다면 최신 버전의 package를 설치해 주는 것입니다.  

중요한 프로젝트를 완료하여 배포하려 한다면, 꼭 glide.yaml 파일에 version을 기록해두는 것이 좋다고 생각합니다.  
그래야 언제든 다시 받았을 때 같은 버전으로 설치가 될 것이기 때문이죠.

> **가장 좋은 것은 glide.lock 파일을 같이 보관하는 것입니다.**  
> **glide.yaml 파일에 버전이 있다고 하더라도 나중에 설치하고 보면 glide.lock 파일이 다른 경우가 발생하곤 합니다.**  
> **그래서 package 충돌로 실행이 안되는 경우가 있습니다.**  

만약 glide.yaml 파일에 version 부분이 없다면 lock 파일에 가서 복붙해도 괜찮습니다.  
꼭 v.1.1.3 같이 들어가지 않아도 좋다는 뜻입니다.  
commit id를 적어 두어도 똑똑하게 찾아갑니다.  
```
- package: google.golang.org/grpc
  version: 3311b9ea68a8c6c1554506af68fcbd9fe3e1c74f
- package: github.com/golang/protobuf
  version: v1.0.0
  subpackages:
  - proto
```

당장은 귀찮을지라도 미래에 버전 충돌로 발생하는 오류를 해결하기 쉬워집니다.  


## glide.lock 파일을 수정해서 특정 버전의 package 사용하기
glide.lock 파일을 열어보면 아래처럼 version이라는 부분이 보일 것입니다.
```
...
imports:
- name: github.com/bradfitz/gomemcache
  version: a41fca850d0b6f392931a78cbae438803ea0b886
  subpackages:
  - memcache
...
```
이것은 내 프로젝트에서 쓰고있는 package 이름이 github.com/bradfitz/gomemcache 이고,  
이 package의 version인 a41fca850d0b6f392931a78cbae438803ea0b886은 git commit id를 뜻합니다.  

최신 버전이 아니라 특정 버전을 사용하고 싶다면 아래 그림처럼 package git으로 가서 쓰고자 하는 버전의 commit id(이미지 빨간 네모 부분)를 찾습니다.  

![github commit id](/assets/images/post/commit_id.png)
그리고 glide.lock의 version 부분을 바꿔줍니다.
```
...
imports:
- name: github.com/bradfitz/gomemcache
  version: 551aad21a6682b95329c1f5bd62ee5060d64f7e8
  subpackages:
  - memcache
...
```
마지막으로  **glide install** 해주면 됩니다.  


## .yaml 파일과 .lock 파일의 version이 다르면 어떻게 될까?!
glide.yaml 파일에는 1.23.1 version을 설치한다고 해두고,
```
- package: google.golang.org/grpc
  version: v1.23.1
```
glide.lock 파일에는 가장 최근 commit id를 써둡니다.
```
- name: google.golang.org/grpc
  version: 3311b9ea68a8c6c1554506af68fcbd9fe3e1c74f
```

이 상황에서 **glide install**하면 어떻게 될까요?  
console의 log를 자세히 보시면 이 부분이 지나가는 것을 알 수 있습니다.  
```
[INFO]  --> Setting version for google.golang.org/grpc to 3311b9ea68a8c6c1554506af68fcbd9fe3e1c74f.
```
yaml 파일은 무시하고 lock 파일의 버전을 따르는 것입니다.  

급하게 버전을 수정해야 할 때엔 lock 파일만 수정하고 **glide install** 해주시면 되겠습니다.  
(그래도 나중을 위해 yaml, lock 최종 버전 맞춤 작업을 해줍시다!~)
혹시 계속 기존 설치되어 있는 버전이 설치된다면 이 명령을 실행하고 install 해주세요.
```
$ glide cc
```
glide가 package를 관리하며 쌓이는 cache들을 지워주는 명령어입니다.  
