---
title: "Git Push 취소하는 방법"
date: 2020-03-20
categories: posts
tags: ["Git"]
---

잘못 Push 한 깃을 취소하려한다.

하지만 그 전에 꼭 같은 팀원과 상의해야하며 자기 마음대로 해버리다간 일이 커진다..

방법은 간단하다. 

말을 취소지만 현재 Branch 의 HEAD 값을 이동시키는 것이다.

또 하나 주의할 점은 기존에 만들어놓은 코드가 HEAD 값의 이동으로 변경된다는것이다.

작업중이던 파일이 있으면 반드시 백업을 만들어놓고 Push 취소를 하자.

```sh
$ ~ git reset --hard HEAD^ or git reset --hard [commit id]

$ ~ git push --force
```