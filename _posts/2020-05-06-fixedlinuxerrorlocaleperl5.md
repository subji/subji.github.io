---
title: "Can't exec \"locale\": No such file or directory at /usr/share/perl5/Debconf/Encoding.pm"
date: 2020-05-06
categories: posts
tags: ["Linux", "Ubuntu"]
---
WSL 을 사용하면서 19.10 에서 20.04 로 판올림을 하는데 중간에 실수로 꺼서 그런지

system 쪽에서 충돌이 발생하여 제목과 같은 오류가 발생하였다.

깨진 파일들을 복구하거나 지우고 새로 설치를 해야된다.

첫번째로 찾은 방법은 다음과 같다.

```sh
  $ apt-get download libc-bin
  $ dpkg -x libc-bin*.deb unpackdir/
  
  $ sudo cp unpackdir/sbin/ldconfig /sbin/
  $ sudo apt-get install --reinstall libc-bin
  $ sudo apt-get install -f
```

명령어는 실행되는데 변화가 없다.

오류도 그대로 발생중이다..

그래서 찾은 다른 방법

```sh
  $ sudo mv /var/lib/dpkg/info /var/lib/dpkg/info_old 　　　　
  $ sudo mkdir /var/lib/dpkg/info 　　　　　　　　　　　　
  $ sudo apt-get update && sudo apt-get -f install
  $ sudo mv /var/lib/dpkg/info/* /var/lib/dpkg/info_old 　　　
  $ sudo rm -rf /var/lib/dpkg/info 　　　　　　　　　　　　
  $ sudo mv /var/lib/dpkg/info_old /var/lib/dpkg/info 　
```

명령어를 보니

1. 기존의 dpkg 내용들을 백업
2. dpkg 내용들을 담을 디렉토리 생성
3. 패키지 업데이트 & 강제 설치
4. 새로 받은 패키지를 백업 패키지 디렉토리에 덮어씌우기
5. 새로 만든 디렉토리 삭제
6. 백업 디렉토리를 원래 패키지 디렉토리로 변경

그냥 쉽게 말해 다시 새로 받은것이다..ㅎ