---
title: "Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #4.Smoke Test"
date: 2019-01-27 23:00:00 -0900
categories: ethereum
tags: 
  - blockchain
  - ethereum
  - dapp
  - solidity
lastmod: 2019-01-27 23:00:00 -0900
---

**본 포스팅은 [dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)을 토대로 작성되었습니다.**

# 목록

1. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #1.Blockchain이란](https://choi3897.github.io/ethereum/ethereum-dapp-1/#)
2. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #2.Smart Contract란](https://choi3897.github.io/ethereum/ethereum-dapp-2/#)
3. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #3.개발환경 설정](https://choi3897.github.io/ethereum/ethereum-dapp-3/#)
4. [**Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #4.Smoke Test**](https://choi3897.github.io/ethereum/ethereum-dapp-4/#)
5. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #5.List Candidates](https://choi3897.github.io/ethereum/ethereum-dapp-5/#)
6. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #6. Cast Votes](https://choi3897.github.io/ethereum/ethereum-dapp-6/#)
7. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #7. Watch Events](https://choi3897.github.io/ethereum/ethereum-dapp-7/#)

# Smoke Test까지 개발하기

---
**Smoke Test**: 구축된 테스트 환경에서 테스트가 가능한지 여부를 판단하기 위해 주요 모듈이나 시스템을 간단하게 테스트하는 것

## Ganache 구동하기

이전 장에서 설치한 Ganache를 구동하면 자동으로 Mining모드로 각각 100개의 가상 이더를 가진 10개의 계정이 로컬 블록체인 네트워크에 생성된다.

Ganache는 CLI에서 설치하고 구동도 가능하지만 본 포스팅은 조금 더 직관적으로 이해하기 쉽도록 GUI버전으로 작성되었다.

이제 우리 프로젝트의 디렉토리를 먼저 생성해보자

```shell
>mkdir election
>cd election
```

election 디렉토리로 이동 후, 우리는 빠르게 dApp 구조를 생성하기 위해 [Truffle Box](https://truffleframework.com/boxes)라는 dApp 프로젝트 샘플 모음을 활용할 것이다. 우리는 이 Truffle Box에서 [Pet Shop Box](https://truffleframework.com/boxes/pet-shop)를 활용할 것이다.

```shell
>truffle unbox pet-shop
```

pet shop box으로 생성된 폴더 구조는 아래 그림과 같다.

<img width="208" alt="eth4_1" src="https://user-images.githubusercontent.com/14902468/51803115-9a418480-2294-11e9-89c1-4a3073f296c6.png">

* contracts 디렉토리
  * 모든 스마트 계약 소스들이 들어가는 디렉토리이다. 우리는 이미 우리 프로젝트의 migrations를 담당하는 Migration이라는 계약코드를 가지게 된다.
* migrantions 디렉토리
  * migration 파일들이 위치하는 디렉토리이다. 웹 개발 프레임워크에서 데이터베이스의 상태를 변경할때 migration이 필요한것과 유사하다.
  * 스마트계약을 블록체인에 배포할때, 이는 블록체인 상태의 업데이트를 의미하기 때문에 migration이 필요하다.
* node_modules 디렉토리
  * 해당 프로젝트에 필요한 모든 Node dependencies가 존재하는 디렉토리이다.
* src 디렉토리
  * Client-side 관련 소스들이 위치하는 디렉토리이다.
* test 디렉토리
  * 스마트 계약의 Test Code들이 위치하는 디렉토리이다.
* truffle.js 파일
  * Truffle 프로젝트의 주요 설정내용이 담겨있는 파일이다.
  
자, 이제 스마트 계약을 실제로 작성해보자. 이 스마트 계약에는 우리가 개발하고자하는 dApp의 모든 비즈니스 로직이 담긴다.

이 계약은 후보자들(candidates) 리스트를 보여주고, 모든 투표와 투표자들을 추적할 수 있는 기능을 가지게 된다. 또한, 선거와 관련된 모든 룰을 규정한다.(예: 계정마다 한번의 투표만 허용 등)

프로젝트의 root 경로에서 contracts 폴더에 우리의 스마트 계약을 생성해보자.

```shell
>touch contracts/Election.sol
```

이제 우리 프로젝트가 제대로 작동하는지 검증할 "Smoke Test"를 작성해보자.

먼저 Election.sol 파일을 열어 아래 code를 작성한다.

```javascript
pragma solidity 0.5.0;

contract Election {
    // Read/write candidate
    string public candidate;

    // Constructor
    
    constructor() public {
        candidate = "Candidate 1";
    }
}
```

위 코드를 해석해보자.

1. 먼저 스마트 계약은 solidity version을 선언하는 pragma solidity 구문으로 시작해야 한다.
2. "contract" 키워드로 계약 이름을 정의한다.
3. candidate 이름을 저장할 string 변수를 정의한다. public으로 설정하게 되면 solidity가 공짜로 해당 변수 내용을 조회할 수 있는 getter 함수를 제공해준다.
4. contructor 함수는 스마트 계약이 블록체인에 배포될 때 최초에 한번 실행된다. 우리가 미리 세팅하고자 하는 변수는 migration을 통해 블록체인에 저장된다.

우리는 지금까지 스마트 계약의 기초 뼈대까지 만들어보았다. 이제 이 코드가 블록체인에 배포될 수 있는지 확인해보자.

이를 위해 우리는 migrations 디렉토리에 새로운 파일을 만들어야 한다.

```shell
>touch migrations/2_deploy_contracts.js
```

migrations 디렉토리에 생성하는 파일들은 모두 숫자로 넘버링해야 Truffle이 순서를 인식하고 실행해주니 꼭 붙여주자.

이제 아래와 같이 우리가 작성한 계약을 배포할 migration을 생성해보자.

```javascript
var Election = artifacts.require("./Election.sol");

module.exports = function(deployer) {
  deployer.deploy(Election);
};
```

위 코드를 살펴보면, 

1. 먼저 우리가 작성한 계약을 요청하여 "Election"이라는 변수에 할당한다. 
2. 그 후, 이 변수를 배포된 계약의 manifest에 추가하여 우리가 migrations를 실행할 때 우리 계약이 배포되게끔 해준다.

이제 migration을 해보자.(Ganache를 미리 실행시켜놓자)

```shell
>cd ~/election
>truffle migrate
```

이제 우리가 작성한 스마트 계약은 로컬 이더리움 블록체인 환경에 성공적으로 migration되었다. 이를 테스트해보기 위해 Console을 열어서 확인해보자.

```shell
>truffle console
```

콘솔에 접속한 후, 우리가 배포한 스마트계약의 instance를 가져와 우리가 계약에 설정한 candidate 이름을 읽어올 수 있는지 확인해보자.
Truffle 콘솔에서 아래 코드를 입력해보자.

```shell
>Election.deployed().then(function(instance) { app = instance })
```

위 코드에서 Election은 우리가 작성한 migration 파일의 변수명이다. deployed() 함수로 배포된 계약의 instance를 가져오고 이를 app이라는 변수에 할당해준다.

이제 우리는 candidate 변수를 아래와 같이 가져올 수 있게 된다.

```shell
>app.candidate()
'Candidate 1'
```

여기까지 우리는 첫번째 스마트 계약을 작성, 블록체인에 배포, 데이터의 검색까지 해보았다.

### 참고

[dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)
