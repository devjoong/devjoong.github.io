---
title:  "예제로 알아보는 Shell Script"
excerpt: "Shell Script를 이용하여 MySQL에 Backup 데이터 집어넣기"

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
쉘 스크립트를 익히기 위해 만든 예시 자료입니다.  
다수의 MySQL 백업 파일을 읽어와 복구하는 상황의 예제입니다.  
사용 방법만 익히신 후 다양하게 활용하시면 됩니다.


## 사전 작업
이 스크립트를 이용하기 위해서는 expect가 설치되어 있어야 합니다.  
설치 방법은 간단합니다.  
```
$ yum install -y expect
```


## 폴더 구조
작업하기 전 폴더 구조를 알아야 코드를 이해할 수 있습니다.
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
backup 폴더 하위에 각 날짜별로 폴더가 있고, 그 아래에 Database(example1, example2, example3) 마다 각각의 백업 파일이 들어 있습니다.


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
$entry만 이용하면 /backup/20200103/example1 이라는 경로를 그대로 보여줍니다.  
example1 이라는 이름만 사용하려면 $(basename "$entry") 를 이용해야 합니다.
- expect <<EOF  
MySQL에 백업 파일을 집어넣으려면 비밀번호를 입력하라는 메시지가 나옵니다.  
그 부분을 자동으로 입력해주기 위해 사용한 expect 입니다.
사전 작업에서 설치한 그것입니다.
- spawn sh -c  
스크립트에서 spawn을 이용하여 db를 넣기 위해서는 **반드시 sh -c 를 붙여서** 사용해야 합니다.
- expect "Enter password:"  
expect 뒤에 나오는 "" 안에는 **spawn sh -c 뒤에 나오는 "" 안의 내용을 shell에 입력하면 나오는 문구와 같은 문구**가 입력되있어야 합니다.  
이 예제에서는 Enter password: 라는 문구가 나오기 때문에 "" 안에 저 문구를 넣어줬습니다.
- send "{password}\r"  
반드시 **\r** 로 끝내야 정상적으로 작동합니다.


## 주의사항
- 10, 11, 12번째 줄에 대한 설명은 꼭 주의해야 합니다.  
- {} 안에는 사용하시는 환경에 맞게 변경한 후 실행해야 합니다.