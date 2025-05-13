# VirtualBox (macOS/Arm)

mac os가 M1 모델 출시 이후로 intel 이 아닌 Arm으로 전환하였기 때문에 가상머신 설치할때 많이 힘들었다.

그래서 보통 UTM이나 vmware fusion, Parallels 등을 사용하였다.

2025.05.12일에 확인한 결과 VirtualBox는 v7.1 버전에서 macOS/Arm 환경에서 지원이 가능해졌다. 

그래서 virtual box를 이용하여 한번 Linux를 설치를 해봤다. 다만 linux를 설치시에는 디스크가 Arm을 지원하는지 체크하자!

결과 사진

![](/assets/img/arm-virtualbox.png)

문제 없이 잘 동작하였다.

### Reference

[Changelog for VirtualBox 7.1](https://www.virtualbox.org/wiki/Changelog-7.1)