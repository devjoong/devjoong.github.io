---
title:  "glide에 대한 설명과 glide install, glide update 두 명령어의 차이점"
excerpt: "golang의 package manager인 glide의 사용법에 대한 설명을 알고 glide install과 update의 차이까지 알아기(상황에 맞는 명령어와 주의사항)"

categories:
  - LearnFromExample
tags:
  - golang
  - go
  - go package manager
  - package manager
  - glide
  - glide install vs update
last_modified_at: 2020-01-12 18:00:00
---
javascript 개발자라면 굉장히 익숙하고 자주 쓸 수 밖에 없는 명령어가 하나 있습니다다.  
바로 **npm**이라는 javascript의 package manager입니다.  

새로은 트렌드 언어답게 golang에도 package manager가 있는데, 여기서 소개할 것은 glide입니다.  
glide를 모르면 엄청난 고생을 할 수 있으니 꼭 알아야만 합니다.  
(예를 들어 다른 사람이 올린 go project 파일을 받아서 실행해보려면 해당 project에 쓰인 package들을 다 받아줘야 하는데, glide를 쓰지 않으면 일일이 파일을 열어서 import를 확인해가며 go get {packagename}을 다 해줘야만 합니다.)  

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
3. 잘못 설치했던 javascript package는 **npm uninstall {package 이름}**으로 지웠었습니다.  
go는 필요 없어진 package를 지울 때 이 명령어를 사용합니다.
실행이 완료되면 install과 마찬가지로 glide.yaml, glide.lock파일이 갱신되고 vendor 폴더에서 해당 package가 삭제됩니다.
```bash
$ glide remove {package 경로}
  or
$ glide rm {package 경로}
```


## glide install vs glide update
npm을 사용할 때 package.json과 package-lock.json 두 파일이 생기는 이유를 아시나요?  
사용하는 package의 목록과 그 package들 각각의 버전을 별도로 관리하기 위해서입니다.

package.json 파일에는 프로젝트에서 사용하는 package 목록이 나열되어 있고,    
(예를 들어 package.json에 A, B, C, D 프로젝트를 사용한다고 되어 있다는 것)  
package-lock.json 파일에는 그 프로젝트들의 버전이 쓰여 있는 것입니다.  
(예를 들어 A는 3.0.1, B는 1.1.8, C는 2.2.3, D는 0.0.1 이라는 버전을 사용중이라고 저장해둔다는 것)  

즉 우리가 javascript 프로젝트를 다운받고 **npm install**이라는 명령어를 실행하면 npm은 package.json에 있는 package를 설치할텐데, package-lock.json에서 그 package의 버전을 찾아서 사전에 사용했던 버전을을 설치할 수 있게 해주는 것입니다.

glide도 같은 방식의 구조를 이루고 있습니다.  
위 2번 설명에서 glide.yaml과 glide.lock 파일이 생성된다는 것을 확인하셨을 것입니다.  
glide.yaml에는 프로젝트에서 사용하는 package 목록이 들어가 있고 glide.lock에는 그 package 각각의 버전이 기술되어 있는 것입니다.
```bash
$ glide install
```
명령을 사용하면 **npm install**처럼 glide.yaml에 있는 package를 vendor에 받아올텐데, glide.lock에서 그 package의 버전을 찾아서 사전에 사용했던 버전을을 받아올 수 있게 해주는 것입니다.  
(glide.lock 파일이 없으면 파일을 생성하고 package의 최신 버전을 받아옵니다.)  

여기서 go 프로젝트를 만든지 오랜 시간이 지났다는 가정 하나만 해보죠.  
시간이 흐른만큼 만들 때 사용했던 package들의 버전이 업데이트된 것들도 상당히 많을 것입니다.  
근데 어떤 package의 버전이 업데이트되고 어떤건 안되었는지 하나하나 확인하기는 상당히 어려운 일입니다.  
이 번거롭고 귀찮은 작업을 해주는 명령어가 update입니다.
```bash
$ glide update
```
vendor 안에 있는 package를 새로운 버전으로 바꿔주고 glide.lock을 그 버전으로 업데이트 해주는 것입니다.  

따라서 **glide install**은 glide.lock에 써있는 package의 버전 그대로 그냥 설치해주는 역할을 하는 것이고 **glide update**는 glide.lock에 써있는 package의 버전을 확인해서 말 그대로 upgrade 해주는 역할까지 한다는 것입니다.


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
위 예시에서는 package 이름이 github.com/bradfitz/gomemcache 이고, 이 package를 만들기 위한 프로젝트에서 a41fca850d0b6f392931a78cbae438803ea0b886 라는 commit 버전을 내 프로젝트에서 현재 쓰고있다는 뜻입니다.  

![github commit id](/assets/images/post/commit_id.png)
최신 버전이 아니라 특정 버전을 사용하고 싶다면 위 package의 프로젝트로 가서 쓰고자 하는 버전의 commit id(이미지 빨간 네모 부분)를 찾아 glide.lock의 version 부분을 바꿔주고 **glide install** 해주면 되는 것입니다.
다음은 변경된 glide.lock 파일 입니다.
```
...
imports:
- name: github.com/bradfitz/gomemcache
  version: 551aad21a6682b95329c1f5bd62ee5060d64f7e8
  subpackages:
  - memcache
...
```


## glide install과 glide update의 효율 차이
그럼 이제 두 명령어의 효율에서 차이가 있음을 알 수 있습니다.  

install은 최신 버전의 유무를 확인하지 않고 glide.lock에 있는 버전을 바로 설치합니다.  
반면에 update는 최신버전이 있는지, 그 버전이 내 vendor 폴더에 들어있는 것과 같은지 확인하고 갱신까지 해주는 역할을 합니다.  

당연히 install이 훨씬 효율적이라는 것을 알 수 있습니다.  
지금 내가 개발중이라면 update 명령어를 쓸 필요 없이 install이라는 명령어를 활용하는 것이 좋은 선택입니다.  


## glide update의 주의사항
어디서 오래된 프로젝트를 받았다고 하더라도 무작정 update를 하는 것이 좋은 것은 아닙니다.  
사용하는 package에 따라서 특정 버전에서만 작동할 수 있기 떄문입니다.  
(예를 들어 받아온 프로젝트가 A package의 3.0.1 버전에서 정상 작동 한다고 합시다. 이때 glide update 명령어를 이용해서 A package의 버전이 5.0.1이 되버리면 deprecated된 부분이 있을 수 있기 떄문에 프로젝트가 정상 작동하지 않게 되는 것입니다.)  

