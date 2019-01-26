---
title: "Ethereum(이더리움) 투표 Dapp Tutorial(번역 및 개발)- #3. 개발환경 설정"
date: 2019-01-26 23:00:00 -0900
categories: ethereum
tags: 
  - Blockchain
  - Ethereum
  - Dapp
  - Solidity
lastmod: 2019-01-26 23:00:00 -0900
---

**본 포스팅은 [dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)을 토대로 작성되었습니다.**

# 목록

1. [Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #1.Blockchain이란](https://choi3897.github.io/ethereum/ethereum-dapp-1/#)
2. [Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #2.Smart Contract란](https://choi3897.github.io/ethereum/ethereum-dapp-2/#)
3. **Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #3.개발환경 설정**
4. Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #4.Smoke Test - Step 1(개발 시작)
5. Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #5.List Candidates - Step 2
6. Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #6. Cast Votes - Step 3
7. Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #7. Watch Events - Step 4(개발 완료)

## 개발환경 설정

---

### Node Package Manager(NPM)

가장 먼저 설치해야할건 node.js기반의 Node Package Manager이다. 버전 상관없이 최신버전으로 받아두면 된다.

자신의 개발환경에 node가 깔려있는지 확인하고자 한다면 아래와 같이 입력하면 된다.

```sh
$ node -v
```

node가 깔려있지 않다면 아래 명령어로 설치하면 된다.

```sh
$ brew(apt-get) install nodejs
```

### Truffle Framework

<span style="color:#4169E1">**Truffle Framework**</span>는 이더리움 블록체인에서 dApp(decentralize Application, 탈중앙화 어플리케이션)을 쉽게 만들게 도와주는 프레임워크다.

Truffle Framework는 Solidity로 스마크 계약을 작성할 수 있는 툴들을 제공해준다.

또한, Truffle은 우리가 작성한 스마트 계약의 테스트와 블록체인으로의 배포를 도와준다. 

뿐만 아니라, 폴더 구조를 미리 만들어 client-side application 소스코드의 공간도 미리 만들어준다.

아래 명령어로 truffle을 설치한다.

```sh
$ npm install -g truffle
```

### Ganache

<span style="color:#4169E1">**Ganache**</span>는 Local in-memory Blockchain 환경이다. [Download Ganache Link](https://truffleframework.com/ganache) 를 통해 설치 가능하다.

Ganache는 10개의 external accounts 주소에 각각 가상의 100 이더(Ether)가 들어있는 로컬 이더리움 블록체인이다.

### Metamask

[Metamask extension for Google Chrome](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en).

<span style="color:#4169E1">**Metamask**</span>는 블록체인을 사용하기 위한(블록체인 네트워크에 접속하기 위한) 도구중 하나이다.

Metamask는 우리의 투표 dApp 개발에 아래와 같은 도움을 준다.

1) Ganache로 구성한 로컬 이더리움 블록체인 네트워크에 Ganache에서 생성된 개인 계정으로 접속
2) 우리가 작성한 스마트 계약을 실행(Transaction 발생 비용 Gas 지불)

### Solidity

truffle로 개발하기 때문에 solc와 같은 Solidity Compiler를 별도 설치하지 않아도 된다.

스마트 계약 작성 시에 Solidity 버전을 명시하게 되는데, 원작자는 solidity^0.4.23 버전을 사용했지만 일부 truffle 호환성 때문에 필자는 <span style="color:#4169E1">**solidity^0.5.0**</span>으로 개발했다.

### IDE

이는 사용자마다 다르겠지만 나는 <span style="color:#4169E1">**Visual Studio Code**</span>(vscode)을 사용했다.

solidity, truffle 등 다양한 Extension으로 스마트 계약 개발을 편리하게 도와준다.

## 필자 개발환경 Version Info

```sh
$ node -v
v11.6.0

$ truffle --version
Truffle v5.0.2 - a development framework for Ethereum
```

solidity version : 0.5.0

### 참고

[dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)
