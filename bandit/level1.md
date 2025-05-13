# bandit level 1

## Quiz

```md
The password for the next level is stored in a file called - located in the home directory
```

## Solution

Quiz를 보면 지난 [level0](level0.md) 문제와 유사하다.

home directory에서 특정 파일 이름을 가진 파일을 찾는 것이다.

다만 파일 이름이 '-'이어서 shell option 명령어로 오인식 될수 있다.

그러므로 패턴 매칭을 할때 ''를 붙여두자.

```zsh
find /home -type f -name '-' | xargs cat
```

이전 난이도 문제와 다른점은 *-type filetype*를 사용 한 점이다.

문제에서 file을 찾는것이기 때문에 -type option을 적용했다.

![](/assets/img/bandit-level1.png)

```md
# password
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```