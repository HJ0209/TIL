# Git

> Git은 분산버전관리시스템(DVCS)의 일종이다.
>
> 소스코드의 이력을 남기고 관리할 수 있다.





## 준비사항

윈도우에서 git을 쓰기 위해서는 [git bash](https://gitforwindows.org/) 에서 다운받아 설치한다.

초기 컴퓨터에 처음 설치하는 경우 commit을 작성하는 사람(author)을 global로 설정을 해야한다.

```bash
$ git config --global user.name HJ0209
$ git config --global user.email blossomfact@gmail.com
```





## 로컬 저장소 활용하기

### 1. 저장소 생성

```bash
$ git init 

Initialized empty Git repository in C:/Users/HPE/Desktop/TIL/.git/
```

* git init 을 입력 (나온 결과 값에 master 표시 있는지 꼭 확인)
* git 저장소를 생성하면, 해당 디렉토리에 `.git`폴더가 생성된다.
* 모든 commit 변화 사항들이 기록된다.
* .git / 를 기준으로 `(master)`는 현재 작업 중인 브랜치가 master라는 의미를 가지고 있다.



### 2. `add`

* working directory (작업 공간) 에서 staging area로 해당 파일을 이동시키기 위해서는 아래의 명령어를 사용한다.

```bash
$ git add markdown.md # 특정 파일
$ git add images/ # 특정 폴더
$ git add . # 현재 디렉토리
```



*  add 전 상태

```bash
$ git status
On branch master

No commits yet
# 트래킹 되지 않고 있는 파일들
# => commit 이력에 한번도 저장되지 않은 파일들, 즉 새로 생성된 파일

Untracked files:
# commit이 될 것들에 포함되기 위해서는 add
# => staging area에 담으려면 add

  (use "git add <file>..." to include in what will be committed)
        git.md
        images/
        markdown.md
 
nothing added to commit but untracked files present (use "git add" to track)
```



* `add`

```bash
$ git add markdown.md
```



* `add`후의 상태

```bash
$ git status
On branch master

No commits yet

# commit 될 변경사항들
# => staging area에 있는 파일들
# => unstage해 working directory로 내리고 싶으면 git rm --cached 파일명 입력
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   markdown.md

# working directory에 있는 파일들
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        git.md
        images/

```





### 3. `commit`

* 이력을 남기기 위해서는 ` $ git commit -m '커밋메시지'` 명령어를 사용한다.

```bash
$ git commit -m 'markdown 활용법 추가'
[master (root-commit) a0ef312] markdown 활용법 추가
 1 file changed, 171 insertions(+)
 create mode 100644 markdown.md
```



* 커밋메시지는 해당 이력을 알 수 있도록 작성하는 것이 중요하고, 항상 일관적으로 작성한다.
  * NHN TOAST Meetup 와 ull.im 블로그 참고



* 커밋 내역은 아래의 명령어로 확인 가능하다

```bash
$ git log
$ git log --oneline #한줄로 표현
$ git log -1 # 최근 것에 대해 한줄. 숫자에 따라 최근부터 갯수대로 확인
```

```bash
$ git log
commit a0ef3125abd8e2a1f9e4fb68dd94b6280f9b1a23 (HEAD -> master)
Author: HJ0209 <blossomfact@gmail.com>
Date:   Fri Dec 27 14:19:08 2019 +0900

    markdown 활용법 추가
```

```bash
$ git log --oneline
a0ef312 (HEAD -> master) markdown 활용법 추가
```

```bash
$ git log -1
commit a0ef3125abd8e2a1f9e4fb68dd94b6280f9b1a23 (HEAD -> master)
Author: HJ0209 <blossomfact@gmail.com>
Date:   Fri Dec 27 14:19:08 2019 +0900

    markdown 활용법 추가
```





## 원격 저장소(Remote Repository) 활용하기

> 원격 저장소를 제공하는 서비스는 gitlab, github, bitbucket 등 다양하나 github을 기준으로 설명한다.



### 1. 원격 저장소 설정하기

```bash
$ git remote add origin github_url
```

* 원격저장소 (`remote`)를 `origin`으로 `github_url`을 추가(`add`)한다.
* 설정된 원격 저장소 목록을 확인하기 위해서는 `git remote -v` 명령어를 활용한다.

```bash
$ git remote -v
origin  https://github.com/HJ0209/TIL.git (fetch)
origin  https://github.com/HJ0209/TIL.git (push)
```

* 설정된 원격 저장소를 삭제하기 위해서는 `git remove rm origin` 명령어를 활용한다.

```bash
$ git remote rm origin
```



### 2. 원격 저장소로 업로드

```bash
$ git push origin master
```

* `origin`으로 설정된 url에 `master` 브랜치로 `push`한다.

