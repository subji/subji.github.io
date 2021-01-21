---
title: "WSL 에서 lxd package broken 으로 인해 apt upgrade 안될 때"
date: 2020-03-12
layout: single
classes: wide
categories: posts
tags: ["wsl"]
---

윈도우 서브 시스템 리눅스 (WSL) 은 snap 이 연동이 안된다.

어느날 apt update && apt upgrade 를 실행하는데 아래와 같은 에러가 발생하였다.

```sh
  Reading package lists... Done
  Building dependency tree       
  Reading state information... Done
  You might want to run 'apt --fix-broken install' to correct these.
  The following packages have unmet dependencies:
  lxd : Depends: lxd-client (= 3.0.3-0ubuntu1~18.04.1) but 1:0.7 is installed
  lxd-client : Depends: lxd (>= 1:0.7) but 3.0.3-0ubuntu1~18.04.1 is installed
  E: Unmet dependencies. Try 'apt --fix-broken install' with no packages (or specify a solution).
```

내용을 보면 lxd, lxd-client 는 각각 3.0.3, 1:0.7 버전 이상을 지원하는데 현재 설치된 버전은 

그 이하여서 안된다는것이다.

그래서 로그에 출력된대로 fix 파라미터를 실행해보았지만 

```sh
  Interacting with snapd is not yet supported on Windows Subsystem for Linux.
  This command has been left available for documentation purposes only.
```

위와 같은 메세지를 출력했다.

내용은 WSL 에서는 snap 을 지원하지 않는다는 얘기같다.

그래서 어떻게 강제로 lxd, lxd-client 를 설치하는 방법이 없을까하고 찾아보다가 

WSL 공식 깃허브에 누군가가 해결방법을 작성해놓았다.


```sh
  sudo dpkg --force depends -P lxd
  sudo dpkg --force depends -P lxd-client
```

dpkg 는 패키지와 관련된 명령어로 설치, 삭제 등을 파라미터로 두고 사용할 수 있다.

--force 옵션은 강제 옵션이다.

-P 옵션은 lxd, lxd-client 의 패키지와 해당 패키지의 설정파일을 삭제한다는 뜻이다. (Purge)

depends-version, depends 옵션은 --force 와 주로 같이쓴다고하는데 첫번째의 경우 버전 의존성 문제를 

경고로 넘어가고 두번째 옵션은 모든 의존성 문제를 경고로 넘어간다는 뜻이다.


위 명령어들을 실행하니 apt update && apt upgrade 가 잘된다!