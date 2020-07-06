---
title: "Git Revert & Reset, 그리고 Working Directory 되돌리기"
date: 2020-07-06
categories: posts
tags: ["git"]
---

### **Intro**
---
Git push 할때 실수로 올라가지 말아야할 것이 올라갔을 때가 있다. 이때 잘못된 내용을 올린 푸시 이전으로 돌아가고 싶을때 사용하는 것이 Reset & Revert 명령어이다. 여기에 더불어 아직 staging 영역에 추가 되지 않은 내용을 다시 되돌리는 방법도 알아보자.

### **Checkout**
---
git checkout {branchName} 으로 자주 사용되는 명령어이다. 하지만 이 명령어로 staging 영역에 올라가지 않는 즉, 커밋은 했지만 변경사항이 추가되지 않았을때 *(git add . 사용 전)* 해당 변경사항을 취소하고 이전으로 돌아갈 수 있도록 도와주기도 한다.

예제를 보자

먼저 기존 내용을 변경하고 git add 이전에 commit 을 먼저하자.

```sh
git commit -m 'Checkout 되돌리기 테스트'
```

<div style="width: 100%; text-align: center;">
  <img src="https://subji.github.io/assets/images/gitrevertresetcheckout1.PNG">
</div>

이 상태에서 변경사항 이전으로 돌아가고 싶으면 다음 명령어를 입력하자.

```sh
git checkout -- {fileName} 
```

위 명령어는 특정 파일을 변경사항 이전으로 돌리는 것이고, 현재 Working Directory 의 모든 파일 또는 특정 Directory 만 돌리고 싶다면 다음처럼 작성하자.

```sh
# Working Directory 내 모든파일
git checkout . 
# 특정 Directory 이름
git checkout {DirectoryName}
```

위 방법은 Staging 영역으로 올라가지 않은 즉, 변경사항이 커밋에 추가되지 않는 건에 대해서만 가능하다. 그렇다면 git add 를 하고 Staging 영역으로 올라간 내용은 어떻게 취소할 수 있을까? 방법은 다음과 같다. 다음에 볼 reset 명령어를 사용할 수 있다. 


```sh
git reset {FileName}
```

이 명령어를 사용하면 변경사항은 유지한채 staging 영역에 올라간 커밋을 취소할 수 있다.

<br>

### **Reset**
---
특정 커밋으로 되돌아가는 명령어. 단, 되돌아간 커밋 이후의 커밋들은 히스토리에서 삭제된다.

**--hard 옵션**
- 특정 Commit 으로 되돌리는 데 변경된 사항도 모두 삭제한다.
```sh
git reset --hard {Commit id}
```

**--soft 옵션**
- 특정 Commit 으로 되돌리는 데 커밋 이력은 삭제하고, 변경사항들에 대해서는 git add 에 올려놓는다.
```sh
git reset --soft {Commit id}
```

**--mixed 옵션**
- 특정 Commit 으로 되돌리는 데 커밋 이력은 삭제되고, 변경사항은 Commit 상태로 변경한다.
```sh
git reset --mixed {Commit id}
```

<br>

### **Revert**
---
특정 커밋으로 되돌아가는 것은 Reset 과 같다. 하지만 되돌아간 커밋 이후의 커밋들은 삭제되지 않고 남아있다.

```sh
git revert {Commit id}
```

Reset 과 비슷하게 특정 커밋으로 되돌아 가지만 커밋 이력을 삭제하지 않고 Revert 라는 새로운 커밋 이력을 남긴다. Reset 의 경우 커밋이력을 삭제하기 때문에 충돌이 발생하지 않지만 Revert 의 경우 커밋 이력이 남기 때문에 충돌이 발생 할 수 있다. 이 부분에 대해서는 직접 수정을 해줘야한다.

<br>

### **Cancel merge**
--- 
개발 브랜치 또는 기타 브랜치들을 master 브랜치로 합치기 위해 merge 를 사용한다. 이때 합쳐지만 안될 내용이 합쳐질떄가 있는데, 이것을 취소할떄도 Revert & Reset 을 사용하면 된다.

Reset 을 사용하는 경우 merge 할 때 최신 커밋에 포인터를 지정한다. (ORIG_HEAD) 그러므로 병합을 취소할 때는 포인터가 가리키는 최신 커밋으로 이동하면 된다. 최신 커밋이란 merge 를 수행할 때, git 에서 합쳐지기 전 마지막 커밋 Id 를 가리킨다.

```sh
git reset -merge ORIG_HEAD
```

위에 처럼 명령어를 사용하면 master 브랜치의 최신 커밋으로 이동하며 merge 가 취소된다. Revert 를 사용하는 방법은 조금 다르지만 간단한 옵션하나만 추가 해주면된다. 단, 여기서 주의할 점은 Rebase 는 git 이력에 merge 에 대한 내용을 남기지 않으므로 되돌릴 수 없다. 

Revert 는 Reset 처럼 마지막 커밋이 merge 일때만 사용할 수 있는 것이 아닌 특정 Merge 에 대해서도 취소할 수 있다. 다음 옵션을 추가하여 사용 할 수 있다.

```sh
git revert -mainline {1/2} {To Cancel Merge Commit ID}
```

mainline 은 merge 취소 후 어느쪽으로 갈것이냐? 즉, master 가 1, 자식 브랜치가 2라고 할때 -mainline 뒤에 1 또는 2 옵션으로 돌아가고자 하는 쪽으로 돌아갈 수 있게 해준다. 숫자는 git 그래프 그림에서 왼쪽부터 1로 매긴다. 

<br>

### **End**
---
reset 은 원격 저장소를 관리할 수 없다. 왜냐하면 커밋이력이 삭제되기 때문이다. 그래서 원격 저장소에 푸시 되기 전에 사용해야한다. revert 는 커밋이력이 존재하기 때문에 푸시가 된 상태에서도 사용할 수 있다. 