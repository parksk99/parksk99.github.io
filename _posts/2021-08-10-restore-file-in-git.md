---
title: git에서 파일 되돌리기
---

# 너무 망쳐버려서 git에 올려놓은 내용으로   돌아가고 싶을때   

***
블로그 카테고리 나누는 작업 중에   절대 안고쳐지는 오류가 생겨서 너무 화남   
그래서 이전 커밋으로 돌아가서 다시 시작하기로 결심   

방법은 아주 아주 간단하다.

```
$git reset --hard HEAD
```
이렇게 하면 가장 최근 커밋으로 돌아갈수 있음.   

끝~~~~~~~~~~~~~~~~~~~~


하지만 너무 날먹 방법이라 검색하며 찾은 내용을 정리해놔야겠다.

# 아주 사소한 내용을 commit 하고싶을 때

***
```
$git commit --amend
```

위 방법은 이전 커밋을 없던 일로 만들고 새롭게 커밋을 하는 방법   
-m 옵션을 안줘서 실행시 commit 메세지를 입력하는 창이 뜨는데   
잘 보면 이전 메세지가 적혀있음.
그래서 그냥 끄면 동일한 메세지로 commit 완료

# reset 사용법

***
일단 reset을 잘 사용하기 위해서는 git구조를 알아야됨   
(git구조를 배우는 계기가 됐다 )

* git의 구조 (branch에 대해 잊으면 이해하기가 더 쉬움)
> 세가지의 트리가 있다고 하는데 받아들이기 어려우니까 세 영역이 있다고 생각하면 될듯
> 1. working directory : 현재 작업중인 로컬 영역
> 2. index(staging area) : git add 했을때 들어가는 영역(commit할 내용을 가지고있다고 보면 됨)
> 3. head : *git repository*안의 commit 중 마지막 commit을 가리킴
> commit한 모든 기록이 git repository안에 저장됨
> file.txt를 세번 commit 했다고 하면 아래 사진과 같음

![git structure](/assets/restore_file_in_git/git_structure.png)

