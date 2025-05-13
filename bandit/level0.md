# bandit level 0

## Quiz

```md
The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.

The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.
```

## Solution

Quiz에서는 SSH, hostname, port, username, password에 대한 정보가 있다.

[먼저 터미널에 다음과 같은 SSH 명령어를 입력하자.](/linux/connect-to-ssh.md)

```zsh
ssh -p 2220 bandit0@bandit.labs.overthewire.org
```

해당 명령어를 입력하고 비밀번호는 quiz에 설명한 비밀번호를 입력하면 접속이 가능하다.

접속을 통과 되면 quiz의 다음 문단을 읽어보자.

level1을 접속하기 위한 비밀번호는 home directory에 readme라고 부르는 파일에 저장되어 있다고 한다.

그러면 먼저 home directory를 찾아보자

```zsh
pwd
```
현재 위치는 /home/bandit0 이다.

현재 위치는 home directory에 위치하니 현재 위치에서 파일들의 목록을 살펴보자.

```zsh
ls -al

# total 4
# -rw-r----- 1 bandit1 bandit0 438 Apr 10 14:22 readme
```

해당 결과를 보니 readme라는 이름을 가진 entry가 존재하며 

*-rw-r-----* 를 살펴보면 첫번째 문자가 '-' 이므로 plain file 이다.

**File type**
```txt
d      if the entry is a directory;

a      if the entry is an append-only file;

D      if the entry is a Unix device;

L      if the entry is a symbolic link;

P      if the entry is a named pipe;

S      if the entry is a socket;

-      if the entry is a plain file.
```

그러므로 해당 readme라는 이름을 가진 파일을 찾았으니 파일 내용을 살펴보자.

```zsh
cat readme

# Congratulations on your first steps into the bandit game!!
# Please make sure you have read the rules at https://overthewire.org/rules/
# If you are following a course, workshop, walkthrough or other educational activity,
# please inform the instructor about the rules as well and encourage them to
# contribute to the OverTheWire community so we can keep these games free!

# The password you are looking for is: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```

결과를 보면 비밀번호를 획득할 수 있다.

![결과 사진](/assets/img/bandit-level0.png)

## Quik solution

엄밀하게 따진다면 문제에서 "home directory"에 readme라는 이름을 가진 파일이 존재한다고 한다. 

그러면 /home 디렉터리안에 수많은 폴더가 있으므로 하위 bandit0 폴더에서 찾는 것이 아니라 /home 디렉터리에서 찾아야한다.

> The password for the next level is stored in a file called readme located in the home directory.

그러므로 *find* command를 사용하자.

```zsh
find /home -name readme | xargs cat
```

![](/assets/img/bandit-level0-quick.png)

home 디렉터리에서 -name option을 사용하여 readme라는 filename 패턴을 찾으면 된다.

그러면 권한이 있는 파일은 오로지 한개의 파일만 출력된다.

물론 문제에서 권한을 풀어야하는 조건이 없기 때문에 이 commend는 유효하다.