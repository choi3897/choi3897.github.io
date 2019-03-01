---
title: "Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #2. Smart Contract란"
date: 2019-01-26 19:00:00 -0900
categories: ethereum
tags: 
  - blockchain
  - ethereum
  - dapp
  - solidity
lastmod: 2019-01-26 19:00:00 -0900
---

**본 포스팅은 [dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)을 토대로 작성되었습니다.**

# 목록

1. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #1.Blockchain이란](https://choi3897.github.io/ethereum/ethereum-dapp-1/#)
2. [**Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #2.Smart Contract란**](https://choi3897.github.io/ethereum/ethereum-dapp-2/#)
3. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #3.개발환경 설정](https://choi3897.github.io/ethereum/ethereum-dapp-3/#)
4. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #4.Smoke Test](https://choi3897.github.io/ethereum/ethereum-dapp-4/#)
5. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #5.List Candidates](https://choi3897.github.io/ethereum/ethereum-dapp-5/#)
6. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #6. Cast Votes](https://choi3897.github.io/ethereum/ethereum-dapp-6/#)
7. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #7. Watch Events](https://choi3897.github.io/ethereum/ethereum-dapp-7/#)

# 스마트 계약(Smart Contract)란

---

## 스마트 계약 기본 개념

1장에서 왜 투표 어플리케이션을 굳이 블록체인을 이용하여 개발하고자 하는지 알아보았다. 그렇다면 투표 기능이 있는 어플리케이션 소스코드가 어떤 원리로 블록체인에서 돌아가는걸까?

이더리움 블록체인은 우리가 짠 코드를 **Ethereum Virtual Machine**(EVM)으로 실행할 수 있게 구성되어 있다. 이 코드가 우리가 오늘 알아보고자 하는 <span style="color:#4169E1">**스마트 계약(Smart Contract)**</span>이다.

스마트 계약은 실제 우리의 어플리케이션이 돌아가는 모든 비즈니스 로직이라고 할 수 있다. 스마트 계약은 블록체인에 데이터를 쓰고 읽는 역할을 하며, 비즈니스 로직들을 실행하는 역할도 할 수 있다. 이는 Javascript와 유사한 <span style="color:#4169E1">**Solidity**</span>라고 하는 프로그래밍 언어로 작성된다.

블록체인상의 스마트 계약 함수(function)들은 웹에서의 Microservice와 유사하다. 공공 장부(public ledger)가 블록체인의 데이터베이스 계층이라면, 스마트 계약(Smart Contract)들은 실시간으로 data에 트랜잭션을 발생시키는 비즈니스 로직이다.

스마트 계약이라고 불리는 또 다른 이유는, 이들이 계약(covenant) 혹은 동의(agreement) 기능을 하기 때문이다. 우리 투표 dApp을 예로 들면, 투표 앱은 :

1) 나의 투표가 정확하게 카운트되고
2) 다른 이들의 투표도 한번만 카운트되며
3) 가장 많은 표를 받은 후보자가 선거에서 승리할 것 
  
  이라는 내용을 사용자간에 서로 합의한 셈이다.

## 투표 dApp 구조

이제 우리가 개발하고자 하는 dApp의 구조를 보자.

![dApp 구조](http://www.dappuniversity.com/dapp_diagram.png)

* 우리는 HTML, CSS와 JavaScript로 구성된 전통적인 Front-end Client를 작성할 것이다. 이 Client는 우리가 설치할 Local Ehtereum Blockchain에 연결될 것이다.
* 우리의 투표 dApp과 관련된 모든 비즈니스 로직들은 Solidity로 작성된 Election이라고 하는 스마트 계약에 작성할 것이다.
* 이 스마트 계약은 우리가 설치한 Local Ehtereum Blockchain에 배포하여 Accounts들이 투표할 수 있도록 할 것이다.

![투표 dApp 화면 예시](http://www.dappuniversity.com/dapp_demo.gif)

* 클라이언트 측에서는 후보자(candidate)들의 리스트를 각각 이들의 id, 이름, 득표수와 함께 테이블에 보여줄 것이다.
* 우리가 지지하는 후보에 투표권을 행사할 수 있는 Form을 만들 것이다.
* 우리가 현재 블록체인에 연결되어 있는 계정 정보를 아래에 보여줄 것이다.

### 참고

[dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)
