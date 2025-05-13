# bandit level 3

## Quiz

```md
The password for the next level is stored in a hidden file in the inhere directory.
```

## Solution

지난 quiz 동안 풀면서 문제에서 주어지는 디렉토리는 전부 /home/bandit[level]인것 같다. 그래서 ssh 접속하면 현재 위치에서 바로 문제를 풀면 되는 것 같다.

문제에 따르면 inhere directory가 있는지 찾아보자.

```zsh
find ./ -type d -name inhere -print
```
inhere라는 디렉토리의 경로가 나왔다. `./inhere`

그러면 inhere 디렉토리에 들어가서 숨긴 파일을 확인해보자. 

```zsh
cd ./inhere && ls -al
```

확인한 결과 `.Hiding-From-You` 파일이 존재한다는 것을 확인 할 수 있다.

그러면 해당 파일을 출력해보자. 

```zsh
cat .*
```
level4의 비밀번호가 나온 것을 확인 할 수 있다.

2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ

![](/assets/img/bandit-level3.png)

```md
# password
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```