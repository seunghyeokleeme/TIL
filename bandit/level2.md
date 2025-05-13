# bandit level 0

## Quiz

```md
The password for the next level is stored in a file called spaces in this filename located in the home directory
```

## Solution

quiz를 내용보면 지난 level 1과 비슷하다.

먼저 spaces가 공백인지 아니면 단어를 의미한지 체크해보자

1. 일단 단어라고 가정하고 다음과 같은 명령어를 입력하자.

```zsh
find /home -type f -name 'spaces' -print
```

해당 명령어를 입력하니 전부 권한이 막혀있는 파일 밖에 없었다.

2. spaces 단어가 포함하는 파일명인지 체크해보자

1번 방식에서는 spaces라는 단어를 포함하고 있는지 체크하기 위해 -name pattern을 다음과 같이 수정하자. *spaces*

```zsh
find /home -type f -name '*spaces*' -print
```

![](/assets/img/bandit-level2.png)

`/home/bandit2/spaces in this filename` 라는 경로에 파일만 권한이 허용되었다.

해당 경로의 파일을 출력하자. 이때 파일명이 공백이 포함되어 있으니 `""`를 붙여서 입력해야한다.

```zsh
cat '/home/bandit2/spaces in this filename'
```

그러면 level3의 비밀번호를 얻을 수 있다.

![](/assets/img/bandit-level2-all.png)

```md
# password
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```