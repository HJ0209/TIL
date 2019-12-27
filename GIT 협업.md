# GIT 협업



## 정보 가져오기

1. Git Hub에서 `clone or download` 에서 https url 복사
2. bash에서 `$git clone github_url`명령어 입력

```bash
$ git clone https://github.com/HJ0209/git_tutorial.git

Cloning into 'git_tutorial'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
```

3. Git Hub의 폴더가 지정된 곳에 다운로드 됨





## PULL

* Clone은 다른 저장소에서 이용하는 것 이다.

* PULL은 다른 장치에서 이용하던 것을 올리고, 다시 가지고 오는 것이 pull이다. 

  (pull은 init의 반댓말이다)

* git init / git clone 중 하나만 선택해서 실행





## PUSH가 안되는 상황 해결 방법

* 원격 저장소 이력과 로컬 저장소 이력이 다른 경우 아래와 같이 push가 되지않는다.

```bash
$ git push origin master
To https://github.com/HJ0209/git_tutorial
 ! [rejected]        master -> master (fetch first)
 # 원격 저장소 이력과 로컬 저장소 이력이 다름
 
error: failed to push some refs to 'https://github.com/HJ0209/git_tutorial'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
# 원격 저장소 변경사항들을 먼저 통합하고 싶을텐데?
# 다시 push 하기 전에 pull을 해봐!

hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

```



* 아래와 같이 해결한다.

```bash
$ git pull origin master
```

pull을 하게 되면,  오류가 난 파일에 이상한 글자가 나타난다.

이를 정리해 올릴 내용으로 마무리한다.

```bash
$ git log --oneline
0a08b3e (HEAD -> master) 학원
75ead10 수정
```



* `pull`을 하는 과정에서 각각 이력이 동일한 파일을 수정했다면, 충돌이 발생한다.
* 충돌이 발생하는 경우 직접 해당 파일을 열어서 수정하고, commit을 한다.

```bash
$ git add .
$ git commit
```

* commit 메시지를 작성할 수 있는 vim이 뜨게 된다.

  `esc`, `:wq`를 순서대로 입력하면 커밋 메시지를 저장할 수 있다. (병합)

* commit 메시지는 자동으로 입력이 되어 있고, 종료한 이후에 다시 `push`한다.

(하나로 합쳐져서 git hub에 올라감)

```bash
$ git push origin master
```

