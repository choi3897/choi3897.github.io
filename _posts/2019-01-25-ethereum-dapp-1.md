---
title: "Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #1.Blockchain이란"
date: 2019-01-25 20:00:00 -0900
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

1. [**Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #1.Blockchain이란**](https://choi3897.github.io/ethereum/ethereum-dapp-1/#)
2. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #2.Smart Contract란](https://choi3897.github.io/ethereum/ethereum-dapp-2/#)
3. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #3.개발환경 설정](https://choi3897.github.io/ethereum/ethereum-dapp-3/#)
4. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #4.Smoke Test](https://choi3897.github.io/ethereum/ethereum-dapp-4/#)
5. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #5.List Candidates](https://choi3897.github.io/ethereum/ethereum-dapp-5/#)
6. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #6. Cast Votes](https://choi3897.github.io/ethereum/ethereum-dapp-6/#)
7. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #7. Watch Events](https://choi3897.github.io/ethereum/ethereum-dapp-7/#)

# 블록체인(BlockChain)이란

---

## 일반적인 Web Application

일반적으로 우리가 Web Application과 상호작용할 때는 웹브라우저를 이용하여 네트워크를 통해 중앙서버에 연결하게 된다.

![일반적인 Web Application](http://www.dappuniversity.com/web_application_diagram.png)

이 어플리케이션의 모든 코드들은 중앙 서버에 존재하게 되며 모든 데이터들 또한 중앙 데이터베이스에 존재한다.

언제든 우리가 어플리케이션에 트랜잭션을 요청, 전송할때에는 "중앙서버"를 통해야만 한다.

하지만 예를 들어, 중앙서버 형태로 웹에 투표 어플리케이션을 만들었을 경우 다음과 같은 문제에 봉착하게 된다:

* 데이터베이스상의 데이터는 조작이 가능하다.
* 웹서버 상의 소스코드는 언제든지 수정될 수 있다.

이는 곧 신뢰받지 못하는 투표 어플리케이션인 셈이며, 그 누구도 이 어플리케이션을 사용하지 않을 것이다.

---

## 블록체인을 활용한 투표 Application

이러한 이유로 우리는 일반적인 웹에서 돌아가는 중앙집권적인 투표 어플리케이션 대신 투표 앱을 블록체인 상에 배포하여 네트워크에 연결된 누구나 투표에 참여할 수 있게 만들고자 한다.
블록체인 상에 배포된 투표 어플리케이션은 모든 트랜잭션 이력이 기록에 남음과 동시에 데이터 조작이 불가능하며, 소스조차 수정이 불가능하기 때문에 사용자들이 믿고 투표할 수 있는 어플리케이션이 될 것이다.

![BlockChain Network](http://www.dappuniversity.com/ethereum_blockchain_nodes_diagram.png)

블록체인은 네트워크와 데이터베이스가 함께 있는 네트워크 종류라고 할 수 있다. 블록체인은 <span style="color:#4169E1">**노드**</span>라고 불리는 컴퓨터들의 P2P(peer-to-peer) 네트워크이며, 모든 데이터와 코드를 블록체인 네트워크 상에서 공유한다.

따라서 나의 디바이스가 블록체인에 연결되어 있다면, 나는 곧 블록체인 네트워크 상의 <span style="color:#4169E1">**노드**</span>인 셈이다. 이렇게 노드가 됨으로써 블록체인 네트워크 상의 모든 컴퓨터 노드들에게 데이터를 전달할 수 있게 된다. 또한, 노드가 되는 순간부터 나는 블록체인 상의 모든 데이터와 코드들의 사본을 갖게 된다.

블록체인은 중앙 데이터베이스와 달리 모든 트랜잭션 데이터(블록체인상의 노드들이 서로 공유하고 있다)들이 <span style="color:#4169E1">**블록**</span>이란 기록들의 묶음에 담기게 된다.

이 블록들은 서로 연결되어 일종의 공공 장부(public ledger)를 형성하게 된다. 이 공공 장부는 블록체인 상의 모든 데이터들을 의미하기도 한다. 이 데이터들은 해싱 암호화로 보호되며 합의(consensus) 알고리즘으로 검증(validation)이 된다.

블록체인 네트워크 상의 노드들에게 분산되어 있는 모든 데이터 사본들은 서로 똑같아야만 한다. 이 때문에 데이터 조작이 힘들어진다. 전 세계에 퍼져 있는 모든 노드들을 조작하는건 사실상 불가능하기 때문이다.

이는 우리가 만들고자 하는 투표 앱에서 상당히 중요한 역할을 한다. 투표자가 투표한 결과가 조작되어서는 안되기 때문이다.

## 일반 유저(투표자) 관점의 블록체인 투표 dapp

일반 유저(투표자)들이 우리가 만들고자 하는 앱에서 투표를 하기 위해서는 이더리움의 암호화폐 이더(Ether)가 들어있는 지갑 주소(Wallet address)가 있는 계정(account)이 필요하다.

투표자들은 투표 앱에 접속 후, 투표권을 행사한 뒤 소정의 트랜잭션 발생 수수료(Gas)을 지불한다.<span style="color:#4169E1">(블록체인에서는 트랜잭션 Read에는 비용이 들지 않지만, 트랜잭션 Write에는 비용이 들게 된다.)</span>

투표 트랜잭션이 발생한 즉시, 블록체인 네트워크상의 일부 노드들이 해당 트랜잭션을 블록에 포함시키며 트랜잭션을 완료시키게 된다. 이들이 바로 <span style="color:#4169E1">**채굴자(Miner)**</span>이다. 해당 트랜잭션을 완료시킨 채굴자는 투표자가 투표 시 지불했던 수수료(Gas)를 보상으로 받게 된다.

### 참고

[dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)
