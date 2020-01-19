---
title:  "버전 붙이는 방법(feat. Semantic Versioning)"
excerpt: "Semantic Version을 이용하여 다른 사람도 내 프로젝트의 버전을 이해할 수 있게 하기"

categories:
  - Tips
tags:
  - version
  - release
  - semantic version
last_modified_at: 2020-01-18 18:00:00
---
Github에 프로젝트를 올리고 Release할 때 어떻게 버전을 붙여아 할 지 고민해본 경험이 있을 것입니다.  
오늘은 버전 네이밍에 대한 얘기를 해보고 싶습니다.  
아무렇게나 막 붙이면 그 버전 네임을 보는 누군가는 오해를 할 수 있습니다.  
굉장히 중요한 이야기지이만 쉽게 들을 수 없던 이야기이기에 공유해보고자 합니다.  
<br>

## 왜 버전 네이밍이 중요할까요?
Module|Sub Module
:---: | :---:
A | aa, ac
B | bb, bd

프로젝트를 진행하며 A, B라는 위의 두 module을 사용했다고 해봅시다.  
간섭을 일으키는 애들을 다 제거하고 정상 실행되는 완성된 상태입니다.  

문제는 시간이 흐르고 유지보수를 위해 다시 이 프로젝트를 작업할 때 나타납니다.  
A, B 모듈들도 당연히 시간이 흐른만큼 업데이트가 진행 되었을테니 그걸 반영하고자 했습니다.  
모듈들을 업데이트 하니 다음과 같아졌네요.  

Module | Sub Module
:---: | :---:
A | aa, ac, ae
B | bb, bd, bc

A에는 ab, B에는 bc라는 sub module이 추가로 사용되었습니다.  
그런데 왠일일까요. B에 새로 추가된 bc는 A의 ac와 충돌하는 것입니다.  
충돌하는 것을 알았다면 A, B module을 업데이트하지 않았을 텐데요.  
혹은 업데이트 하더라도 확인해보고 했겠지요?  

이런 문제를 미리 알고 예방할 수 있는 방법이 버전 네이밍을 잘 이해하고 사용하는 것입니다.  
<br>

## 그렇다면 어떻게 이해해야 할까요?
굉장히 쉽습니다.  
버전은 . 2개로 나눠서 총 3부분으로 구분하여 사용합니다.  
맨 앞 숫자를 **Major**, 두번째 숫자를 **Minor**, 세 번째 숫자는 **Patch**로 이해하면 됩니다.  
이것을 **Semantic Versioning**이라고 합니다.  

예를 들어 보겠습니다.
> 3.1.2

지금 버전에서 발생하는 버그만 수정한 경우에는 세 번째 숫자인 Patch 부분만 하나 올리면 됩니다.  
기존 모든 코드가 잘 호환되어야 합니다.  
> 3.1.3

여기서 또 발견되는 새로운 버그를 수정하고, 몇 가지 기능을 추가하면 가운데 부분을 올립니다.  
여기서도 역시 기존의 코드와 무도 호환되야 합니다.  
> 3.2.1

마지막 Major입니다.  
이 부분은 기존의 코드들과 호환되지 않아도 되는 부분입니다.  
일정 부분이 deprecated되어 사라지고, 새로운 기능이 추가되는 큰 업데이트를 나타냅니다.  
> 4.0.1

<br>

## 시작은 어떻게 할까요?
개발 중에는 
> 0.0.1  

처럼 Major 부분이 0으로 시작하면 됩니다.  
그 후에 위 규칙처럼 차례차례 올려나가면 됩니다.  
<br>

정식으로 첫 배포를 하고자 할 때,
> 1.0.1

으로 정식 배포가 완료되는 것이지요.  
이러한 약속을 지키지 않는 분을 본다면 함께 지켜나갈 수 있도록 알려주시기 바랍니다.  

모두 Semantic Version 약속을 지켜나갑시다.  
<br>

## 참고
더 자세한 내용을 참고하고 싶으신 경우에는 아래 링크를 참고해주세요.  
> [https://semver.org/](https://semver.org/)