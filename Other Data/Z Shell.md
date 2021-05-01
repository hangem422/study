# Z Shell

이미 기본쉘로도 많은 것이 가능하지만 Z Shell을 사용한다면 훨씬 더 강력하게 사용할 수 있습니다. **Oh My ZSH**는 Z Shell에 여러가지 플러그인이나 추가 기능을 붙일 수 있도록 만들어진 플러그인입니다.

## 1. 장점

이쁘다라는 이유만으로도 충분하지만 사실 많은 기능이 있습니다. 대표적 기능은 아래와 같습니다.

### 1.1 경로 자동추론

`/u/l/n`만 입력해도 `/usr/local/vin`이 자동으로 입력됩니다.

### 1.2 타이핑 교정

`git add`를 잘못 입력하여 `git adb`로 입력한다면 자동으로 `add`일 수 있다고 알려줍니다.

### 1.3 명령어 추천

`git a`를 입력하고 탭을 누르면 a로 시작하는 명령어들을 추천해줍니다. 이 외에 아래와 같은 플러그인을 추가하여 자동 완성을 할 수 있습니다.

### 1.4 다양한 플러그인

다양한 플러그인을 조합할 수 있습니다. **git**과 **docker**의 경우 명령어를 자동으로 추천해줍니다.

## 2. 설치하기

### 2.1 ZSH 설치

#### 2.1.1 Mac

```bash
brew install zsh
chsh -s `which zsh`
echo $SHELL
```

#### 2.1.2 Linux

```bash
apt-get update
apt-get install zsh
chsh -s `which zsh`
echo $SHELL
```

### 2.2 Oh my ZSH 설치

#### 2.2.1 wget을 사용한 설치

```bash
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

#### 2.2.2 curl을 사용한 설치

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## 3. 테마 변경하기

테마는 그 종류가 매우 다양합니다. 그 중에서도 **agnoster** 테마를 많이 사용합니다. 이 테마는 현재 디렉토리에서 **Git**의 상태를 알려줍니다. 이 기능은 보이는 것보다 아주 강력해서 현재 마스터브랜치인지 개발브랜치인지 혹은 커밋을 까먹고 하지 않았는지 등을 자연스럽게 알려줍니다.

### 3.1 테마 변경을 위한 .zshrc 파일 수정

1. 파일 열기
   vi ~/.zshrc
2. line 11: ZSH_THEME을 agnoster로 변경
   ZSH_THEME='agnoster'
3. 변경사항 저장 후 터미널에서 .zshrc 파일 업데이트
   source ~/.zshrc

## 4. 폰트 깨짐 해결을 위한 폰트 설치

agnoster 태머룰 솔차 허셨다면, 폰트 깨짐 현상이 발생합니다. power-line 폰트 설치를 통해 이 문제를 해결할 수 있습니다.

### 4.1 Powerline

```bash
sudo apt install fonts-powerline
```

### 4.2 D2Coding

[https://github.com/naver/d2codingfont](https://github.com/naver/d2codingfont)

## 5. 플러그인 설치

### 5.1 Syntax Hightlight

사용할 수 없는 명령어라면 하이라팅 되지 않습니다.

```bash
brew install zsh-syntax-highlighting
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

## 6. 출처

- [Oh My ZSH+ iTerm2로 터미널을 더 강력하게 - 해리의 유목코딩](https://medium.com/harrythegreat/oh-my-zsh-iterm2%EB%A1%9C-%ED%84%B0%EB%AF%B8%EB%84%90%EC%9D%84-%EB%8D%94-%EA%B0%95%EB%A0%A5%ED%95%98%EA%B2%8C-a105f2c01bec)
- [[Linux] 기본 터미널을 z shell(zsh)로 바꾸고 oh my zsh 적용해서 스타일링 하기 - Teddy's DEV Note](https://teddylee777.github.io/linux/linux%EC%97%90%EC%84%9C-%EA%B8%B0%EB%B3%B8%ED%84%B0%EB%AF%B8%EB%84%90-%EC%8A%A4%ED%83%80%EC%9D%BC%EB%A7%81%ED%95%98%EA%B8%B0)
