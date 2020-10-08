---
title: "CentOS 에서 Redis 여러개 실행하기"
date: 2020-10-08
categories: posts
tags: ["redis"]
---

### **Topic**
---
master-slave-sentinal 구조로 Redis 를 구성하기 위해서 여러 방법을 생각해봤다. 첫째는 하나의 서버에 여러개의 slave 서버를 띄우는 방법. 두번째는 여러개의 서버에 각각 하나씩 slave 를 띄우는 방법.

각각의 장단점은 있다. 첫번째는 여러 서버를 확인하지 않아도 되는 관리하는 관점에서 쉽다는 이점이 있다. 두번째는 그래도 첫번째보다는 더 나은 안정성? 이 있을 수 있겠다.

Redis 를 여러개로 구성하는 이유에 따라 나뉘겠지만, 나는 여기서 Redis 를 단순히 세션 클러스터링으로 사용하기로 했기 때문에 하나의 서버에 여러개의 Redis 를 띄우기로 했다.

<br>

## **How to solve?**
---
**1. redis rdb 파일이 작성되는 디렉토리 생성**
  - rdb 파일 경로의 경우 Redis 에서 자동으로 생성해주지 않기 때문에 직접 만들어 주고 사용자, 그룹 권한(redis) 도 주어야 한다.
  - 다음은 redis2 라는 새로운 서버를 생성하는 경우의 예제이다.
  ```shell
    mkdir -p /var/lib/redis2/
    chown redis /var/lib/redis2/
    chgrp redis /var/lib/redis2/
    # 또는
    chown redis:redis /var/lib/redis2/
  ```
**2. 설정 파일 추가**
  - 기본으로 설치된 설정 파일을 복사해 몇가지만 수정한다.
  - slaveof 에서 master 의 host 와 port 를 적는다.
  - masterauth 와 requirepass 에 master 에서 설정한 패스워드를 적는다.
  - 나머지는 구글링으로 필요한 내용을 작성했다.
  - 사용자 권한을 부여한다. 권한을 부여하지 않으면 실행 스크립트에서 오류가 발생한다.
  ```shell
    cp /etc/redis.conf /etc/redis2.conf
    chown redis /etc/redis2.conf

    slaveof 127.0.0.1 6379
    masterauth {password}
    requirepass {password}
    --- redis2.conf
  ```
**3.서비스 스크립트 생성**
  - 서비스 스크립트의 실행 시작, 종료 부분을 변경한다.
  - daemonize no 를 추가하지 않으면 데이터 삽입 시 충돌이 발생한다.
  ```shell
    cp /usr/lib/systemd/system/redis.service /usr/lib/systemd/system/redis2.service

    ------ redis2.service file ------
    [Service]
    ExecStart=/usr/bin/redis-server /etc/redis2.conf --daemonize no
    ExecStop=/usr/bin/redis-shutdown redis2
    ---------------------------------

    systemctl enable redis2
    systemctl start redis2
  ```

<br>

## **참고**
---

[How to reload authorities on user update with Spring Security](https://stackoverflow.com/questions/9910252/how-to-reload-authorities-on-user-update-with-spring-security)