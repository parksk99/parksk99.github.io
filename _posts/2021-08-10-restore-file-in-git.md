---
title: git에서 파일 되돌리기
---

# 너무 망쳐버려서 git에 올려놓은 내용으로 돌아가고 싶을때   

***
블로그 카테고리 나누는 작업 중에   
절대 안고쳐지는 오류가 생겨서 너무 화남   
그래서 이전 커밋으로 돌아가서 다시 시작하기로 결심   

방법은 아주 아주 간단하다.

```
$ git reset --hard HEAD
```
이렇게 하면 가장 최근 커밋으로 돌아갈수 있음.   
```
$ git reset --hard HEAD~2
```
이렇게 하면 두개 전 커밋으로 돌아간다.   

끝~~~~~~~~~~~~~~~~~~~~


하지만 너무 날먹 방법이라 검색하며 찾은 내용을 정리해놔야겠다.

# 아주 사소한 내용을 commit 하고싶을 때

***
```
$ git commit --amend
```

위 방법은 이전 커밋을 없던 일로 만들고 새롭게 커밋을 하는 방법   
-m 옵션을 안줘서 실행시 commit 메세지를 입력하는 창이 뜨는데   
잘 보면 이전 메세지가 적혀있음.   
그래서 그냥 끄면 동일한 메세지로 commit 완료   
* 단, 이미 push했을 경우 약간 귀찮아짐   
   
   
# reset 사용법

***
일단 reset을 잘 사용하기 위해서는 git구조를 알아야됨   


* git의 구조 (branch에 대해 잊으면 이해하기가 더 쉬움)
> 세가지의 트리가 있다고 하는데 받아들이기 어려우니까   세 영역이 있다고 생각하면 될듯
> 1. working directory : 현재 작업중인 로컬 영역
> 2. index(staging area) : git add 했을때 들어가는 영역(commit할 내용을 가지고있다고 보면 됨)
> 3. head : *git repository*안의 commit 중 마지막 commit을 가리킴   
> commit한 모든 기록이 git repository안에 저장됨  
 
file.txt를 세번 commit 했다고 하면 아래 사진과 같음

![git structure](/assets/restore_file_in_git/git_structure.png)   

## git reset --soft HEAD~

```
$ git reset --soft HEAD~   
```

soft옵션 사용시 head만 이전 커밋으로 바뀐다.   
HEAD가 file.txt (Ver.2)를 가리키고 있다.
![soft](/assets/restore_file_in_git/git_reset_soft.png)   

## git reset --mixed HEAD~

```
$ git reset --mixed HEAD~   
```

head와 index가 이전 커밋으로 바뀐다.   
아무 옵션 없을 때($ git reset HEAD~)와 동일한 기능   
INDEX의 file.txt가 Ver.3에서 Ver.2로 바뀐 모습
![mixed](/assets/restore_file_in_git/git_reset_mixed.png)   

## git reset --hard HEAD~
```
$ git reset --hard HEAD~
```

head와 index와 working dir모두 이전 커밋으로 바뀐다.   
working dir의 file.txt가 Ver.3에서 Ver.2로 바뀐 모습
![hard](/assets/restore_file_in_git/git_reset_hard.png)   

# 결론

***
* 이미 push 했을 경우 귀찮아짐   
github에 있는 파일과 working dir에 있는 파일이 달라지니까 merge를 해줘야됨   
*푸시도 중요할때 한번*   
* category 만들다가 화나서 복구하는 와중에   
잘 모르고 썼던 git에대해 배우게 됨...   
* rebase는 머지?
