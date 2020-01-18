---
title:  "script에서 for 사용 방법과 비밀번호 입력시키는 방법"
excerpt: "Shell Script의 expect를 이용하면 원하는 시점에 비밀번호를 입력시킬 수 있음"

categories:
  - LearnFromExample
tags:
  - 복구
  - 쉘
  - 쉘스크립트
  - 스크립트
  - bash
  - db
  - expect
  - for
  - insert
  - mariadb
  - mysql
  - recover
  - restore
  - script
  - shell
last_modified_at: 2020-01-06 22:00:00
---
shell script를 작성하고 사용할 때 비밀번호를 넣어주고 싶은 경우가 있습니다.  
예를들어 MySQL 백업 파일을 읽어와 복구하는 상황이 그런 경우입니다.  


## 사전 작업
당연스럽게 expect가 설치되어 있어야 합니다.  
```bash
$ yum install -y expect
```


## 폴더 구조
아래 코드의 예제를 이해가기 위해서 폴더 구조를 먼저 확인하고 가겠습니다.  
backup 폴더 하위에 각 날짜별로 폴더가 있고, 그 아래에 Database(example1, example2, example3) 마다 각각의 백업 파일이 들어 있습니다.
```
root
 | 
 +-- backup
 |  |  
 |  +-- 20200103
 |     |
 |     +-- example1
 |        |
 |        +-- backup.sql.gz
 |     +-- example2
 |        |
 |        +-- backup.sql.gz
 |     +-- example3
 |        |
 |        +-- backup.sql.gz
 |  +-- 20200104
 |  +-- 20200105
```


## 스크립트 코드와 설명
```bash
for entry in "/backup/20200103"/*;
do
    echo $(basename "$entry")
    
    cp /backup/20200103/$(basename "$entry")/backup.sql.gz ./$(basename "$entry")_backup.sql.gz
    gzip -d $(basename "$entry")_backup.sql.gz

    expect <<EOF
    set timeout 3
    spawn sh -c "mysql -h {ip} -P {port} -u {id} -p {database name} < $(basename "$entry")_backup.sql"
    expect "Enter password:"
    send "{password}\r"
    expect eof
EOF
done
```
- for entry in "/backup/20200103"/*;  
/backup/20200103/ 하위 모든 폴더를 가져와서 그 개수만큼 for문을 돌립니다.  
하위 폴더나 파일 이름을 가져오는 방법은 매우 많습니다.  
다른 방법을 사용할 경우 그 아래 이름을 넣어주는 부분을 같이 수정해야합니다.  

- $(basename "$entry")  
위 방법에서는 $entry를 이용하면 /backup/20200103/example1 이라는 경로를 그대로 보여줍니다.  
example1 이라는 이름만 사용하고자 한다면 $(basename "$entry") 를 이용해야 합니다.  

- expect <<EOF  
MySQL에 백업 파일을 집어넣으려면 비밀번호를 입력하라는 메시지가 나옵니다.  
그 부분을 자동으로 입력해주기 위해 사용한 expect 입니다.  
expect의 뜻 그대로 어떤 글이 나오길 기대하다가 그때 원하는 행동을 해달라는 것입니다.  

- spawn sh -c  
스크립트에서 spawn을 이용하여 db를 넣기 위해서는 **반드시 sh -c 를 붙여서** 사용해야 합니다.  

- expect "Enter password:"  
expect 뒤에 나오는 "" 사이에 우리가 비밀번호를 넣기 바로 전 글을 입력해줍니다.  
우리가 비밀번호가 들어가길 expect 하는 부분이 여기야! 를 알려주는 것입니다.  

- send "{password}\r"  
반드시 **\r** 로 끝내야 정상적으로 작동합니다.


## 보충설명
```bash
$ mysql -h 192.168.112.11 -u root -p test < test.sql
Enter password:
```
복구 명령어를 실제 쉘에서 그냥 입력하면 "Enter password:" 라는 부분이 보입니다.  
이 부분이 expect 다음 "" 사이에 입력되어야 하는 글입니다.  
mysql 비번 입력이 아닌 script 돌릴 때 **사용자의 입력이 필요한 모든 부분**에서 응용 가능합니다.  