# Build node environment in linux

## **g++ 최신버전 설치**

centOS 6.5 에서 최대 설치 가능 g++ 버전은 4.4.7 입니다. yum 을 통해 더 상위 버전의 g++ 를 설치해줍니다.

### yum repository에 정보 추가하기.

curl [http://linuxsoft.cern.ch/cern/scl/slc6-scl.repo](http://linuxsoft.cern.ch/cern/scl/slc6-scl.repo) &gt; /etc/yum.repos.d/slc6-scl.repo

### /etc/yum.repos.d/slc6-scl.repo 파일 성정 변경

파일에서 gpgcheck=1 을 gpgcheck=0 으로 수정합니다.

```bash
vi /etc/yum.repos.d/slc6-scl.repo
```

### devtoolset-3를 설치

```bash
yum install -y devtoolset-3
scl enable devtoolset-3 bash
```

### g++ 버전확인

```bash
g++ -v
```

## **node 환경구축**

설치 순서는 nvm 설치 후 nvm을 통해 node와 npm을 설치한다.

### nvm 설치

curl 혹은 weget으로 설치한다. 이 때 설치 버전은 [nvm 공식 사이트](https://github.com/creationix/nvm)를 통해 확인하자.

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

설치가 완료되면 다음 라인이 ${HOME}/.bashrc 에 들어가 있다.

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

nvm 환경 적용을 위해 .bash\_profile 에 .bashrc를 자동으로 시작하는 루틴을 넣어준다.

```bash
vi ~/.bash_profile

if [ -f ~/.bashrc ]; then
. ~/.bashrc
fi
```

### node 설치

[node.js 공식 사이트](https://nodejs.org/ko/)에서 최신 릴리즈 확인 후 nvm으로 다운로드.

```bash
nvm install v12.13.0
nvm use v12.13.0
```

버전 자동 선택을 위해 다음 라인을 ${HOME}/.nvm\_profile 에 추가해준다.

```bash
echo "nvm use v10.15.0" >> ${HOME}/.nvm_profile
source ${HOME}/.nvm_profile
```

