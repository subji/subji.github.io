---
title: "WSL1 cannot read realtime clock: Invalid argument 오류"
date: 2020-06-09
categories: posts
tags: ["wsl", "linux"]
---

WSL 을 사용하다가 sleep 명령어에서 다음과 같은 오류가 발생하였다.

"sleep:cannot read realtime clock: Invalid argument" 

실제 시간이 안맞춰져서 인가? 싶어 찾아보았다.

WSL 20.04 이전버전에서 발생하는 버그로 20.04 로 업데이트 하는 과정 또는 그 이전 버전에서 발생하는 버그였다.

그래서 20.04 로 설치되면서 꼬인 라이브러리들을 재 설치 하고 sleep 명령어도 재 복사 해주었다.


```sh
# 잘못 설치된 라이브러리 수정
wget https://launchpad.net/~rafaeldtinoco/+archive/ubuntu/lp1871129/+files/libc6_2.31-0ubuntu8+lp1871129~1_amd64.deb
sudo dpkg --install libc6_2.31-0ubuntu8+lp1871129~1_amd64.deb
sudo apt-mark hold libc6
sudo apt --fix-broken install
sudo apt full-upgrade
```

```sh
# sleep 명령어 복사
sudo -i
sudo mv /bin/sleep /bin/sleep~ ; touch /bin/sleep ; chmod +x /bin/sleep
```