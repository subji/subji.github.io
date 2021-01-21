---
title: "Apache 의 동작 방식"
date: 2020-02-13
layout: single
classes: wide
categories: posts
tags: ["apache"]
---

## | Apache? 
우선 아파치는 HTTP Server 로 오픈소스이다.
<br>
Nginx 도 많이 사용하지만 아직까지는 Apache 의 사용률이 우세인것 같다.
<br>
보통은 Web Application Server 인 톰캣등에서 사용되는 정적 리소스 (Static Resource) 들을 처리할때 사용한다.
<br>
또한 모듈을 추가하여 사용할 수도 있다. 대표적으로 SSL 과 같은 모듈을 사용하여 HTTPS 를 적용하곤한다.

## | Apache 의 동작
아파치는 다양한 환경, 플랫폼에서 사용할 수 있게 설계되었다.
<br>
<br>
그래서 각각의 다른 환경, 플랫폼에 대응할 수 있게 모듈화된 설계를 사용했다.
<br>
<br>
이처럼 유연한 모듈화된 설계방식을 아파치에서는 시스템쪽으로도 확장하여 
<br>
네트워크의 처리를 Root 와 Child 를 두어 분산시키는 **다중처리모듈(Multi-Processing Modules, MPMs)** 를 사용하였다.
<br>
<br>
아파치의 동작 방식은 **http -V** 를 입력하여 확인할 수 있다.
```bash
$ http -V

  Server version: Apache/2.2.15 (Unix)
  Server built:   Jul 23 2014 14:17:29
  Server's Module Magic Number: 20051115:25
  Server loaded:  APR 1.3.9, APR-Util 1.3.9
  Compiled using: APR 1.3.9, APR-Util 1.3.9
  Architecture:   64-bit
  Server MPM:     Prefork
    threaded:     no
      forked:     yes (variable process count)
  Server compiled with....
```
위 명령어에 대한결과에서 보면 MPM 이라고 적힌 부분 옆의 **Prefork** 가 현재 구동중인 아파치의 동작방식이며
<br>
아파치 설치 후 초기의 동작방식이다.
## | Apache 동작 방식의 종류
### 1. Prefork
  - 요청 하나당 하나의 프로세스를 할당하는 방식
  - 요쳥에 대한 처리가 각각의 프로세스에서 처리되므로, 하나의 프로세스에서 오류가 발생해도 다른 프로세스에 영향을 
  미치지 않는다.
  - 요청에 많아짐에 따라 프로세스가 늘어나므로 시스템의 메모리, CPU 등 리소스에 부하가 생길 가능성이 높다.
### 2. Worker
  - 각각의 요청에 대해 프로세스가 여러개가 생성되는 것이 아닌 프로세스의 쓰레드를 여러개 생성하여 할당하는 방식
  - 다른 요청이라도 같은 프로세스내의 쓰레드를 사용하므로 메모리를 공유하게 된다. 그러므로 서로 영향을 미치게 된다.
  - Prefork 에 비해 적은 프로세스를 사용하므로 시스템 리소스의 부하가 비교적 적다.
  - 단일 프로세스에서 여러개의 쓰레드를 관리하므로 프로세스에 오류가 발생했을 경우에 그 안의 쓰레드에 영향을 미친다.
  - 한 프로세스의 쓰레드 한계치에 해당하는 대량의 연결이 발생하는 서버에서 적합하다.
### 3. Event
  - nginx 에 비해 뒤떨어지는 퍼포먼스로 인해 Apache 2.4 버전부터 추가된 동작 방식
  - 이전의 두 방식은 프로세스가 요청에 대해 연결이 끊어지기 전까지 계속 연결을 유지하였다. 이와 같은 방식은 대량 접속의 경우에는 많은 시스템 리소스의 손실이 오게 된다.
  - Event 방식에서는 이러한 문제를 해결하기 위해 요청을 받는 쓰레드를 분리하여 분산화하였다.

## | Apache 동작 설정
/etc/httpd/conf/httpd.conf 파일 또는 /etc/apache2/conf/httpd.conf 파일에서 설정이 가능하다.
<br>
위 파일을 열어보면 Prefork 와 Worker 밖에 안보이는데, Event 방식은 Worker 와 설정이 같기 때문에 
<br>
Event 방식을 적용하고자 한다면 Worker 의 설정을 수정해주면된다.
```bash
# prefork MPM
# StartServers: number of server processes to start
# MinSpareServers: minimum number of server processes which are kept spare
# MaxSpareServers: maximum number of server processes which are kept spare
# ServerLimit: maximum value for MaxClients for the lifetime of the server
# MaxClients: maximum number of server processes allowed to start
# MaxRequestsPerChild: maximum number of requests a server process serves
<IfModule prefork.c>
  StartServers       8
  MinSpareServers    5
  MaxSpareServers   20
  ServerLimit      256
  MaxClients       256
  MaxRequestsPerChild  4000
</IfModule>
# worker MPM
# StartServers: initial number of server processes to start
# MaxClients: maximum number of simultaneous client connections
# MinSpareThreads: minimum number of worker threads which are kept spare
# MaxSpareThreads: maximum number of worker threads which are kept spare
# ThreadsPerChild: constant number of worker threads in each server process
# MaxRequestsPerChild: maximum number of requests a server process serves
<IfModule worker.c>
  StartServers         4
  MaxClients         300
  MinSpareThreads     25
  MaxSpareThreads     75
  ThreadsPerChild     25
  MaxRequestsPerChild  0
</IfModule>
```