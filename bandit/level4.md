# Bandit Level 4

## Quiz

```md
The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
```

## Solution

먼저 inhere directory에 들어가서 파일 목록들을 확인해보자

```zsh
cd ./inhere && ls -l

# total 40
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file00
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file01
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file02
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file03
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file04
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file05
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file06
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file07
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file08
# -rw-r----- 1 bandit5 bandit4 33 Apr 10 14:23 -file09
```
해당 목록에서 총 10개의 plain 파일들이 존재한다.

여기서 어떤 파일들이 human-readable file인지 찾아봐야한다.

일단 human-readable이면 아스키코드로 작성된 txt일 것이다.

그래서 일단 목록 중에 모든 파일들(-file*)의 정보를 확인해보자

```zsh
file ./-*

# ./-file00: PGP Secret Sub-key -
# ./-file01: data
# ./-file02: data
# ./-file03: data
# ./-file04: data
# ./-file05: data
# ./-file06: data
# ./-file07: ASCII text
# ./-file08: data
# ./-file09: data
```

목록 중에 ASCII text의 정보를 가진 파일은 하나만 존재하는 것을 발견했다.

해당 파일을 출력해보자

```zsh
cat ./-file07
```
level5의 비밀번호가 나온 것을 확인 할 수 있다.

![](/assets/img/bandit-level4.png)

```md
# password
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```