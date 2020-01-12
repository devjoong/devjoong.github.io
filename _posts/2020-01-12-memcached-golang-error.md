---
title:  "golang과 memcached를 연결할 때 발생할 수 있는 오류"
excerpt: "golang의 github.com/bradfitz/gomemcache/memcache package를 이용하여 memcached에 연결했을 때 쉽게 발생하지만 모르면 해결할 수 없는 오류"

categories:
  - TipsForError
tags:
  - golang
  - go
  - go client
  - memcached
  - memcached server
  - error
  - 오류
  - connect
  - timeout
  - GCP
  - Google Cloud Platform
last_modified_at: 2020-01-12 12:00:00
---
golang을 이용해서 memcached에 연결하며 발생했던 오류에 대한 이야기입니다.  
상당히 심플한 오류지만, 모르면 하루 종일 헤맬 수 있습니다. 제가 그랬구요...ㅜㅜ  
해결 방법을 알게되면 자괴감과 허탈함이 쑥쑥 자라나죠.  
오래 헤매지 말고 쉽게 해결합시다!

## 상황 설명
Google Cloud Platform의 Compute Engine(us-central region)에 Memcached 서버가 올라가 있습니다.  
그 서버에 golang client를 연결하려는 상황입니다.

golang과 memcached를 연결해주는 package로는 github.com/bradfitz/gomemcache/memcache을 이용했습니다.  
PC에 해당 package를 받아줍니다.
```
$ go get github.com/bradfitz/gomemcache/memcache
```

[공식 사용법](https://github.com/bradfitz/gomemcache)을 보시면 아시겠지만 사용법은 아래와 같이 매우 심플합니다.  
이 package를 이루고 있는 구조에 대해서 보시려면 [여기](https://godoc.org/github.com/bradfitz/gomemcache/memcache#Client.Add)에서 확인 가능합니다.
```go
import (
        "github.com/bradfitz/gomemcache/memcache"
)

func main() {
     mc := memcache.New("10.0.0.1:11211", "10.0.0.2:11211", "10.0.0.3:11212")
     mc.Set(&memcache.Item{Key: "foo", Value: []byte("my value")})

     it, err := mc.Get("foo")
     ...
}
```
그냥 import하고 New function을 이용해서 서버와 연결한 뒤 사용하면 됩니다.  


## 오류
문제는 New에서 연결이 성공했는지 실패했는지 오류를 return하지 않는다는 것입니다.  
Set function을 써봐야 오류인지 아닌지 알 수 있습니다.  
저는 Set 부분에서 계속 **connect timeout**오류가 발생했습니다.

## 문제 원인
위 상황 설명처럼 저의 GCP Compute Engine은 us-central region을 이용하고 있었습니다.
ping 때려보면 평균 응답 시간으로 160ms 정도(매우 느리네요) 나옵니다.  
이 속도가 문제가 **주요 문제 원인** 이었습니다.  

golang과 memcached 연결을 위해 쓴 해당 package의 코드를 뜯어보니 default timeout 시간 설정이 100ms였던 것입니다.
```
DefaultTimeout = 100 * time.Millisecond
```

## 해결 방법
그래서 해결 방법이 간단했던 것입니다.  
단지 default timeout 시간 설정을 사용하지 않고 자신의 서버 상황에 맞게 바꿔주면 되는 것입니다.  
아래의 코드를 이용해서 말이죠.
```go
mc.Timeout = 10 * time.Second
```
넉넉히 잡아 보기 위해 Second를 이용했지만 Millisecond로 조정해줘도 됩니다.
완성된 코드를 보면 아래처럼 됩니다.
```go
import (
        "github.com/bradfitz/gomemcache/memcache"
)

func main() {
     mc := memcache.New("10.0.0.1:11211", "10.0.0.2:11211", "10.0.0.3:11212")
     mc.Timeout = 10 * time.Second

     mc.Set(&memcache.Item{Key: "foo", Value: []byte("my value")})

     it, err := mc.Get("foo")
     ...
}
```

# 결과
golang에 미숙하기도 한데 GCP도 미숙해서 발생했던 문제가 아닌가 싶습니다.  
로컬에서 telnet을 이용하면 잘되는데 golang만 쓰면 안되서 GCP 설정 문젠(예를 들어 방화벽 관련?)가 싶어 한참을 헤맸습니다.  
심지어 nodejs 로 다시 client를 작성하니 잘 되더군요...ㅜㅜ  
golang 코드 문제임을 확신하게 된 순간이었습니다.  

**메뉴얼에는 없지만 같은 문제로 헤메지 않기를 바라며 기록해둡니다.**