---
title:  "저 선배는 실행되는데 나는 안되는 1가지 이유"
excerpt: "선배가 정상 작동한다고 github에 올려준 javascript 프로젝트를 받았는데, 내 pc에서는 정상적으로 작동하지 않는 경우 확인해볼 부분 1가지"

categories:
  - TipsForError
tags:
  - javascript
  - npm
  - package
  - package-lock
  - react
  - vue
  - nodejs
  - error
  - 오류
  - git
  - 필수
last_modified_at: 2020-01-13 22:00:00
---
javascript 프로젝트 관련하여 협업을 하다보면 상대방이 테스트까지 완료한 잘 된다는 파일이 정작 내 pc에서는 잘 안되는 경우가 있습니다.  
많은 이유가 있겠지만, [지난번 포스팅]({{site.url}}/learnfromexample/glide-install-vs-update/)과 관련된 한 가지 경우에 대해 말해보고자 합니다.  

우리는 javascript 프로젝트를 다운받으면 제일 먼저 하는 행동이 있습니다. 바로 이거죠.  
```bash
$ npm install
```  

그런데 이 명령어와 관련된 파일이 2가지가 있었습니다.  
바로 package.json과 package-lock.json 입니다.
1. package.json : 해당 프로젝트에서 사용하는 package 들의 목록  
2. package-lock.json : package.json에 있는 package 들 각각의 버전  
<br/>  

----
잠시 golang package manager인 glide로 돌아와서  
**glide install** 명령어에서 glide.lock 파일이 없는 경우 어떻게 되는지 기억 나시나요?  

네, glide.lock 파일을 생성하고  
glide.yaml 파일에 있는 package 들의 최신 버전을 설치한다고 했었습니다.  

npm 역시 마찬가지입니다.  
package-lock.json 파일이 없다면, package-lock.json 파일을 생성하고 package.json 파일에 있는 package 들의 최신 버전을 설치하는 것입니다.

----
<br/>
예를 들어 A라는 package가 있다고 생각해봅시다.  
최신 버전은 4.0.1이며 이 버전에서는 2.x.x 버전에 있던 add라는 함수가 deprecated 되었습니다.  
하지만 작업 중인 프로젝트에는 add라는 함수가 꼭 필요해서 2.x.x 버전의 A package를 사용했습니다.  
정상적으로 작동하는 것을 확인한 후 파일을 github에 업로드하는데 package-lock.json 파일은 빼고 package.json 파일만 업로드했습니다.  
이 프로젝트를 다운받아 실행한 사람은 package들을 설치하기 위해 npm install 명령어를 사용했지만, package-lock.json 파일이 없기 때문에 A package가 최신 버전인 4.0.1 버전으로 설치되었습니다.  
안타깝게도 프로젝트에 필요한 add라는 함수가 없어 정상 작동하지 않게 됩니다.

> 앞으로는 javascript 프로젝트가 정상 작동하지 않으면 꼭 **package-lock.json** 파일때문은 아닌지 확인해봅시다.  

