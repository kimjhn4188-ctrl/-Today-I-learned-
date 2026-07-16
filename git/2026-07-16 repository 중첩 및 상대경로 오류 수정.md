# shared.git 을 기반으로 가상의 개발자2명이 협업&충돌을 해결하는 상황을 시뮬레이션.
------------------------------------------------------------------------------
# 문제상황1: 잘못된 명령어와 오타로인한 오류 해결.

```bash
:~$ rmdir work-m3
rmdir: failed to remove 'work-m3': Directory not empty
```
rmdir work-m3 -> **rm -rf work-m3** (work-m3 directory가 비어있지 않은 상태로, 강제 삭제 명령어로 수정)

```bash
:~$ git config  --global pull.rebase false
:~$ mkdir -p work-m3 && cd work -m3
bash: cd: too many arguments
```
mkdir -p work-m3 && cd work -m3 -> **mkdir -p work-m3 && cd work-m3**(work-m3 스페이스바 수정)

-----------------------------------------------------------------------------------------------------
# 문제상황2 : repository 중첩 및 상대경로 오류 해결.

```bash
:~/work-m3$ git init --bare shared.git
Initialized empty Git repository in /home/kimjhn4188/work-m3/shared.git/
:~/work-m3$ mkdir -p user1 && cd user1
:~/work-m3/user1$ git init
Initialized empty Git repository in /home/kimjhn4188/work-m3/user1/.git/
:~/work-m3/user1$ git remote add origin ../shared.git
:~/work-m3/user1$ mkdir -p user1 && cd user1
:~/work-m3/user1/user1$ git init

:~/work-m3/user1/user1$ git push -u origin master
fatal: '../shared.git' does not appear to be a git repository
fatal: Could not read from remote repository.
```
__원인 분석__
1. 상위 폴더 ~/work-m3에서 첫 번째 mkdir -p user1 && cd user1을 실행하여 정상적으로 ~/work-m3/user1 경로에 진입함.
2. 이후 동일한 명령어(mkdir -p user1 && cd user1)를 한 번 더 입력하게 되면서, user1 폴더 내부에 또 다른 하위 폴더 user1이 생성되고 그 안으로 진입함. (~/work-m3/user1/user1)
3. 이 상태에서 원격 저장소 주소를 상대 경로(../shared.git)로 등록함.
4. 중첩 경로가 생성되어 work-m3/user1/user1의 상위 폴더인:~/work-m3/user1을 가리키게 됨. 이 폴더 내부에는 shared.git이 존재하지 않기 때문에 Git이 원격 저장소를 찾지 못해 fatal 에러를 발생시킴.

__해결__

1. work-m3/user1/user1의 상위 폴더work-m3/user1로 이동
> cd ..

2. 잘못생성된 user1 폴더 삭제.
> rm -rf user1

3. work-m3/user1에 저장소 재설정.
> git init

> git remote add origin ../shared.git

4. 소스코드 재작성 후, commit 성공 확인.
> git add doc.txt

> git commit -m 'user1 문서'

> git push -u origin master


