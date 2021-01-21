---
title: "Git 에러 error: pathspec { branch 명 } did not match any file(s) known to git."
date: 2020-05-20
layout: single
classes: wide
categories: posts
tags: ["git"]
---

Repository 에 새로운 브랜치를 생성하고 사용하던중 다른 피시에서 새로 생성된 브랜치를 불러오는데

제목과 같은 오류가 발생하였다.

다른 피시에 있는 브랜치는 새로 생성된 브랜치를 가지기 전의 브랜치로 이를 업데이트해줘야 했다.

방법은 다음 명령어를 실행해보자

```sh
git remote update
git fetch
git checkout {branch}
```