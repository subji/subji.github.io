---
title: "Intellij 열린 에디터 선택 시 해당 파일 가리키기 Select opened file or editor"
layout: single
classes: wide
date: 2021-02-25
categories: posts
tags: ["intellij"]
---

# 문제
이전에 vscode 를 사용하면서 열린 파일을 선택했을때, 왼쪽에 Sidebar 에서 자동으로 파일로 포커싱이 되는 기능이 있었다. 프로젝트 구조가 길어지면서 스크롤을 하면서 찾아가기 힘들때 아주 많은 도움이 되었었다.
이번에 Intellij 를 사용하면서 당연히 해당 기능이 있을거라 생각했는데, 내가 무슨 설정을 잘못한건지 아니면 기본값이 그런건지 동작하지 않았다.
뭐 물론 Ctrl + Shift + N 을 눌러서 파일을 직접 찾는 방법도 있다. 하지만 그냥 지금 열린 파일을 자동으로 프로젝트 구조에서 선택되게 해주면 더 편하기에 방법을 이리저리 찾아보았다.

</br>
</br>
</br>

# 해결방법
방법은 간단하다. 수동으로 포커싱 해주는 단축키도 있다(Alt + f1). 하지만 그보다 항상 자동으로 되는걸 바랬기에 다음 설정을 체크하도록 한다.

<div style="width: 100%; text-align: center;">
  <img src="https://subji.github.io/assets/images/2021-02-25-intellijselectopeneditor_0.png" style="width: 80%">
</div>
