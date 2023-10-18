# Node.js

[Node.js Official Site](https://nodejs.org/ko)

## Node.js 가 뭐지

- Chrome V8 JavaScript 엔진으로 빌드된 **JavaScript 런타임**
- **JavaScript를 서버에서도 사용**할 수 있다.
- 서버사이트 스크립트 언어가 아니다. **프로그램**이다.
- 웹서버와 같이 **확장성** 있는 네트워크 프로그램을 제작할 수 있다.
- **다양한 자바스크립트 애플리케이션**을 실행할 수 있다.
- Non-blocking I/O와 단일 스레드 이벤트 루프를 통한 높은 처리 성능
- 내장 HTTP 서버 라이브러리를 포함하고 있어 웹 서버에서 아파치 등의 별도 소프트웨어 없이 동작하는 것이 가능

## nvm or fnm

- Node.js 버젼 관리도구

**nvm 설치**

`$ brew install nvm`

**nvm 을 환경변수로 등록 과정 (bash or zsh)**

`$ vi ~/.bash_profile`

`$ vi ~/.zshrc`

```zsh
bash

export NVM_DIR="$HOME/.nvm"
[ -s "/usr/local/opt/nvm/nvm.sh" ] && . "/usr/local/opt/nvm/nvm.sh"
[ -s "/usr/local/opt/nvm/etc/bash_completion" ] && . "/usr/local/opt/nvm/etc/bash_completion"
```

```zsh
zsh

export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```

**환경 변수 적용**

`$ source ~/.bash_profile`

`$ source ~/.zshrc`

**nvm 자주 쓰는 명령어**

```
nvm install ${node_version}
nvm install --lts // lts 최신버전
nvm ls
nvm -v
nvm use ${node_version}

nvm alias default node
nvm alias default version
```
