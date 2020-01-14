---
title:  "glide 기본 사용법과 yaml, lock파일 파헤치기"
excerpt: "golang의 package manager인 glide의 사용법을 npm과 비교하며 알아보고 glide.yaml, glide.lock 파일을 알아보자"

categories:
  - LearnFromExample
tags:
  - golang
  - go
  - go package manager
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
(예를 들어 다른 사람이 올린 go project 파일을 받아서 실행해보려면 해당 project에 쓰인 package들을 다 받아줘야 합니다.  
**go get -u -v ./...** 같은 명령어를 이용하여 한 번에 받을 수 있지만 package들의 버전 관리가 안된다는 단점이 있습니다.)  

설치 방법은 [이곳](https://glide.readthedocs.io/en/latest/) 하단에서 확인할 수 있으며 자세한 방법은 구글링해도 매우 많습니다.  
쉬운 이해를 이해 간단히 npm과 비교하며 설명하겠습니다.


## 사용 방법
1. javascript 프로젝트 내에 package.json 파일이 없다면 무엇을 하시나요? 아마 **npm init**부터 할 것입니다.  
go 역시 프로젝트 내에 glide.yaml 파일이 없다면 이 명령어 부터 시작하면 됩니다.  
위 명령어를 실행하면 해당 프로젝트 파일들을 모두 읽으며 import되어 있는 package들, package에 쓰이는 또 다른 package들을 모두 확인하여 glide.yaml 파일을 만들어줍니다.  
```bash
$ glide init
```
2. javascript 프로젝트에 **npm install {package 이름}**을 이용해서 package를 설치했었죠?  
go 역시 필요한 package를 설치할 때 이 명령어를 사용합니다.  
install이 아니라 get임을 주의해야 합니다.  
실행이 완료되면 glide.lock 파일이 새로 생기고 glide.yaml 파일과 함께 설치한 package가 등록될 것입니다.  
그리고 vendor라는 폴더가 생겼을 텐데 이 폴더는 javascript 프로젝트의 **node_modules**와 같은 폴더입니다.
```bash
$ glide get {package 경로}
```
3. git에서 javascript 프로젝트를 새로 받으면 **npm install**을 해줬었습니다.  
2번에서 get을 사용한 이유가 여기서 install을 쓰기 때문입니다.  
이 명령어도 2번의 명령어처럼 glide.lock 파일, vendor라는 폴더가 새로 생깁니다.  
```bash
$ glide install
```
4. 잘못 설치했던 javascript package는 **npm uninstall {package 이름}**으로 지웠었습니다.  
go는 필요 없어진 package를 지울 때 이 명령어를 사용합니다.
실행이 완료되면 install과 마찬가지로 glide.yaml, glide.lock파일이 갱신되고 vendor 폴더에서 해당 package가 삭제됩니다.
```bash
$ glide remove {package 경로}
  or
$ glide rm {package 경로}
```
5. 마지막으로 볼 명령어는 npm에 없는 기능인 것 같네요.  
이 명령어는 glide.lock 파일에 있는 package의 버전과 git의 release된 버전을 확인해서 최신 버전이 있는지 검사해주는 역할을 합니다.  
물론 새롭게 선택한 버전을 glide.lock 파일에 업데이트 해주며 vendor 폴더에도 그 버전으로 넣어줍니다.
```bash
$ glide update
```


## glide.lock의 특징과 특정 버전의 package 사용하기
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

최신 버전이 아니라 특정 버전을 사용하고 싶다면 아래 그림처럼 package git으로 가서  
쓰고자 하는 버전의 commit id(이미지 빨간 네모 부분)를 찾아  
glide.lock의 version 부분을 바꿔주고 **glide install** 해주면 됩니다.  

![github commit id](/assets/images/post/commit_id.png)

version 부분을 위 그림의 빨간 네모(commit id)로 바꾼 것을 알 수 있습니다.
```
...
imports:
- name: github.com/bradfitz/gomemcache
  version: 551aad21a6682b95329c1f5bd62ee5060d64f7e8
  subpackages:
  - memcache
...
```

## glide.yaml의 특징
작성 예정
