---
title: "Windows10 여러개의 VPN 설정하는 방법"
date: 2020-05-06
categories: posts
tags: ["vpn", "windows"]
---
작업을 하다보면 여러개의 VPN 을 사용해야 할 때가 있다.

매번 하나의 연결을 끊고 다시 연결하고를 반복할 수가 없는 노릇이다.

그래서 이번에는 여러개의 VPN 을 연결하는 방법을 알아보자.

먼저 TAP-Windows 어댑터를 추가해줘야한다.

TAP-Windows 어댑터란 가상 네트워크 커널 장치로 하드웨어 네트워크 어댑터가 아닌 소프트웨어 차원에서 지원된다.

Open VPN 의 경우 해당 어댑터를 사용하는데 기본적으로 하나의 어댑터가 추가되어있고 여러개의 VPN 을 사용하기 

위해서는 TAP-Windows 를 늘려주면된다.

그 방법은 아래와 같다.

먼저 아래의 경로로 이동한다.

```cmd
  C:\ProgramData\Microsoft\Windows\Start Menu\Programs\TAP-Windows\Utilities
```

그러면 아래와 같이 TAP-Windows 를 추가 / 삭제할 수 있는 배치 파일이 보인다.

<div style="width: 100%;">
  <img src="https://subji.github.io/assets/images/20200506_1.PNG" /> 
</div>

가상 TAP 어댑터를 추가해야 하니 "Add a new TAP virtual ethernet adapter" 파일을 우클릭하여 관리자로 실행시키자.

<div style="width: 100%;">
  <img src="https://subji.github.io/assets/images/20200506_2.PNG" /> 
</div>

다음과 같은 진행 상황 화면이 나오며 "계속하려면 아무 키나 누르십시오..." 문구가 나오면 아무버튼이나 눌러서 종료한다.

여기까지 됬으면 가상의 TAP-Windows 어댑터가 추가된것이다.

이제 확인하러 "제어판 - 네트워크 및 인터넷 - 네트워크 연결" 으로 이동해보자.

보면 "로컬 영역 연결 1 ~ 4" 부분이 새롭게 추가된 TAP 어댑터이다.

이제 VPN 파일(.opvn) 을 열어 다음과 같은 명령어를 추가해주자.

```shell
  dev-node "TAP-Windows 이름" # 여기서는 로컬 영역 연결 1 ~ 4
```

마지막으로 VPN 연결 해주었던 방식대로 연결해주면 여러개의 VPN을 사용할 수 있게 된다.

