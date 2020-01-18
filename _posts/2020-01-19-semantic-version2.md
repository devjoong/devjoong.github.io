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
[지난 포스팅에서 설명한 Semantic Versioning]({{ site.url }}/tips/semantic-version2/)과 관련하여 package manager에서는 어떻게 표기되고 있는지 이해해보고자 합니다.  
<br>

## 이해해야 할 부분
그냥 v2.1.0처럼 써 있는 것은 더 이해하고 말고 할 것도 없습니다.  
우리가 알아야 할 것은 ^기호와 ~기호 뿐입니다.  
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

## ^, ~ 기호의 의미
