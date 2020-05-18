---
title: "Crontab 적용 시 주의사항(Precautions to apply Crontab)"
date: 2020-01-15
categories: posts
tags: ["Linux"]
---

## | 문제
ShellScript 파일을 Crontab 에 적용하는데 있어서 실행이 안되는 경우가 발생하였다.

## | 해결
- 가급적이면 'vi crontab' 대신 ```crontab -e' 를 사용하자 'vi' 로 실행하는 경우는 계정 권한에 따른 설정이 달라지는데, 'crontab -e' 로 실행하는 경우는 이를 고려하지 않아도 되기 때문이다.
- Crontab 에는 절대경로 / 환경변수 설정을 꼭 해주어야 한다.
- 가급적이면 ShellScript 로 만들고, 파일 상단에 '/bin/bash, /.../bin/python' 등과 같이 환경변수 설정을 해주자
- ShellScript 는 이왕이면 실행가는한 파일로 만들자. 
```shell 
  chmod +x 스크립트파일.sh
```
- Crontab 의 최소 시간은 1분이며, 초단위로 하고자 할때는 * * * * sleep {second}; {명령어} 의 형태로 사용하자.