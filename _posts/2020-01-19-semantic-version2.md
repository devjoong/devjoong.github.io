---
title:  "package.json 또는 glide.yaml 파일의 version 부분(feat. Semantic Versioning)"
excerpt: "package manager에서 기본 생성되는 package.json 또는 glide.yaml 파일에서 Semantic Version이 쓰인 부분 이해하기"

categories:
  - Tips
tags:
  - version
  - release
  - semantic version
  - glide.yaml
  - package.json
last_modified_at: 2020-01-18 18:00:00
---
[지난 포스팅에서 설명한 Semantic Versioning]({{ site.url }}/tips/semantic-version2/)과 관련하여 이 표기법을 package manager에서는 어떻게 사용하고 있는지 이해해보고자 합니다.  
<br>

## 이해해야 할 부분
그냥 v2.1.0처럼 써 있는 것은 더 이해하고 말고 할 것도 없습니다.  
우리가 알아야 할 것은 ^기호와 ~기호입니다.  
이것이 이번 포스팅의 의미, 이유입니다.  

바로 예를 들며 시작하겠습니다.  
package.json이나 glide.yaml 파일을 보면 version이라는 부분을 볼 수 있습니다.  
아래 코드는 제가 하던 프로젝트의 glide.yaml 파일의 일부분입니다.  
```
- package: github.com/patrickmn/go-cache
  version: ^2.1.0
- package: github.com/mailgun/mailgun-go
  version: ~3.6.4
```

하나는 ^ 기호로 다른 하나는 ~ 기호로 표기되어 있습니다.  
과연 무엇이 다른 걸까요?  
<br>

## 기호 ^ 와 ~ 의 의미
Semantic Version 표기법에서 1.2.3 버전은 Major 1, Minor 2, Patch 3으로 이해할 수 있다는 것을 알 수 있었습니다.  
각 부분별 명칭을 기억해야 기호의 의미를 완전히 이해할 수 있습니다.  

1. ~ 기호  
~ 기호는 Minor 버전 제한을 걸겠다는 의미입니다.  
다시 말해서 Patch만 제약없이 업데이트하고 Minor 업데이트는 하지 않겠다는 의미이죠.  
위 예제에서 mailgun-go의 버전이 ~3.6.4라고 써있었죠?  
이것은 3.6.4 이상, 3.7.0 미만의 버전을 사용하겠다는 의미입니다.  
3.6.5, 3.6.6, ... , 3.6.10 ... 등 Patch 업데이트는 계속 사용 하겠다는 것이죠.  
하지만 Minor 업데이트가 된 3.7.0부터는 업데이트하지 않는다는 것입니다.

2. ^ 기호  
반면에 ^ 기호는 Major 버전 제한을 걸겠다는 의미입니다.
다시 말해서 ~ 기호보다 한 단계 높은 Minor만 제약없이 업데이트하고 Major 업데이트는 하지 않겠다는 의미이죠.
위 예제에서 go-cache의 버전이 ^2.1.0으로 써있었죠?
이것은 2.1.1, 2.1.2 같은 Patch 업데이트 뿐만 아니라 2.2.3, 2.3.2, ... , 2.11.3 등 Minor 업데이트까지는 계속 사용하겠다는 것입니다.  
Major 업데이트가 되는 3.0.0 부터 업데이트 하지 않는다는 것을 의미하지요.  
<br>

## 참고
더 자세하고 깊은 내용을 알고 싶으신 경우엔 아래 링크를 참고해주시면 되겠습니다.  
제 설명은 . 2개로 구분된 버전만 되어 있지만 링크에 들어가시면 .이 없거나 1개인 경우도 나와 있습니다.  
의미를 알고 쓴다는 것은 차이가 있으니 한 번 봐두세요~  
> [https://glide.readthedocs.io/en/latest/versions/](https://glide.readthedocs.io/en/latest/versions/)