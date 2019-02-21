---
title: "Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #5.List Candidates - Step 2"
date: 2019-02-21 23:00:00 -0900
categories: ethereum
tags: 
  - Blockchain
  - Ethereum
  - Dapp
  - Solidity
lastmod: 2019-02-21 23:00:00 -0900
---

**본 포스팅은 [dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)을 토대로 작성되었습니다.**

# 목록

1. [Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #1.Blockchain이란](https://choi3897.github.io/ethereum/ethereum-dapp-1/#)
2. [Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #2.Smart Contract란](https://choi3897.github.io/ethereum/ethereum-dapp-2/#)
3. [Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #3.개발환경 설정](https://choi3897.github.io/ethereum/ethereum-dapp-3/#)
4. [Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #4.Smoke Test - Step 1(개발 시작)](https://choi3897.github.io/ethereum/ethereum-dapp-4/#)
5. **Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #5.List Candidates - Step 2**
6. Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #6. Cast Votes - Step 3
7. Ethereum(이더리움) 투표 Dapp Tutorial 개발 - #7. Watch Events - Step 4(개발 완료)

# 후보자(Candidates) 리스트 불러오기

---

## Election.sol 수정하기

이전 장까지 이상없이 잘 따라왔다면 이제 스마트 계약을 개발할 준비가 다 된 셈이다.

### 후보자 Modeling

먼저 선거에 출마할 후보자들을 리스트 형태로 화면에 뿌려보는 것부터 해보자.

이를 위해 우리는 여러 후보자(candidate)들을 저장하고 각 후보자마다 여러 특징들을 저장해야 한다. 각 후보자에게는 후보자 번호(id), 이름(name), 득표수(voteCount)라는 속성을 부여하고자 한다.

이를 위해 우리가 일전에 Election.sol 파일에 작성한 Election 계약에서 아래와 같이 후보자를 모델링할 수 있다.

```javascript
contract Election {
    // Model a Candidate
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // ...
}
```

후보자 모델링은 [Solidity Struct](https://solidity.readthedocs.io/en/v0.4.21/structure-of-a-contract.html?highlight=struct#structure-struct-types)에서 요구하는대로 만들어졌다. 위 후보자 struct에서는 id를 unsigned integer type, name을 string type, voteCount를 unsigned integer type으로 구성하였다.

이렇게 struct만 candidate 인스턴스가 생기는 것은 아니다. 스토리지에 후보자들 정보를 저장하기 위해선 우리가 방금 정의한 후보자 struct의 인스턴스를 저장할 수 있는 변수가 하나 필요하다.

우리는 [Solidity mapping](https://solidity.readthedocs.io/en/v0.4.21/types.html?highlight=mapping#mappings)이라는 것을 활용해보자. Solidity에서 mapping 데이터 타입은 array 혹은 hash의 집합체로 key-value 형태로 저장된다. 아래와 같이 후보자들을 저장하는 mapping을 선언해보자.

```javascript
contract Election {
    // Model a Candidate
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // Read/write Candidates
    mapping(uint => Candidate) public candidates;

    // ...
}
```

위 코드에서 mapping의 key값은 unsigned integer 데이터 타입이며, value값은 우리가 조금 전에 정의한 Candidate 구조체다. 이로써 우리는 후보자들의 id로 후보자를 조회할 수 있게 되었다.

우리가 만든 후보자 mapping은 contract 하위에 위치한 <span style="color:#4169E1">**state variable(Java에서의 전역변수와 유사)**</span>(반대되는 개념: local variable)이기 때문에 새로운 key-value로 언제든지 블록체인에 데이터를 기록할 수 있다. 또한 **public**으로 선언했기 때문에 getter function을 자동으로 갖게되어 외부에서 함수호출만으로 조회가 가능하게 된다.

### 후보자 Counter 선언하기

또한, 아래와 같이 counter cache state variable을 선언하여 선거에 참여하는 후보자들의 수를 알 수 있다.

```javascript
contract Election {
    // Model a Candidate
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // Read/write Candidates
    mapping(uint => Candidate) public candidates;

    // Store Candidates Count
    uint public candidatesCount;

    // ...
}
```

Solidity에서는 mapping의 사이즈를 알 수 없으며 iterator로 전체 데이터를 조회하는게 불가능하다. 이는 value값이 정의되지 않은 임의의 key값을 mapping으로 조회해도 default값을 반환해주기 때문이다.(우리 예제에서는 빈 candidate struct를 반환하게 된다.)

### 후보자 추가하는 함수 생성

이제 아래와 같이 후보자들을 추가하는 함수를 생성해보자.

```javascript
contract Election {
    // ...

    function addCandidate (string memory _name) private {
        candidatesCount ++;
        candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
    }
}
```

addCandidate 함수 내부에서는 후보자 수를 나타내는 candidatesCount를 증가시켜주고, 이 증가된 수를 id로 사용하여 후보자 mapping에 새로운 Candidate struct를 할당한다. name은 파라미터로 받은 값, vote count는 초기값으로 0을 준다. 이 함수는 contract 내부에서만 호출할 수 있도록 **private**으로 선언되었다.

### EVM의 3가지 저장 영역

위에서 파라미터를 선언할때 **memory**라는 키워드를 선언했는데, EVM(Ethereum Virtual Machine)에서는 3가지 저장 영역이 존재한다.
1) "storage" : contract state variable이 존재하는 영역. 모든 계약마다 각자의 storage가 있다. storage는 함수간에 공유되며 호출시에 다소 비싼 사용료(Gas)가 필요하다.
2) "memory" : 임시 값을 저장하는 영역이다. (외부)함수 호출간에 사라지며 사용료가 다소 저렴하다.
3) "stack" : 작은 local variable을 저장하는데 사용된다. 사용료가 대부분 무료이며, 제한된 양의 값들만 유지할 수 있다.

### 2명의 후보자 생성

이제 아래와 같이 계약이 배포될 때 2명의 후보자를 함께 생성하도록 만들어보자.

```javascript
contract Election {
    // ...

    function Election () public {
        addCandidate("Candidate 1");
        addCandidate("Candidate 2");
    }

    // ...
}
```

여기까지 우리가 작성한 계약코드는 블록체인에 deploy될 때 migration되어 두 명의 후보자를 생성하며 실행될 것이다.

### 최종코드(Election.sol)

지금까지 이상없이 잘 따라왔다면 contract code는 아래와 같이 되어있을 것이다.

```javascript
pragma solidity 0.5.0;

contract Election {
    // Model a Candidate
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // Read/write candidates
    mapping(uint => Candidate) public candidates;
    // Store Candidates Count
    uint public candidatesCount;

    constructor() public {
        addCandidate("Candidate 1");
        addCandidate("Candidate 2");
    }

    function addCandidate (string memory _name) private {
        candidatesCount ++;
        candidates[candidatesCount] = Candidate(candidatesCount, _name, 0);
    }
}
```

여기까지 완성됐다면 계약코드를 아래와 같이 migrate해보자

```shell
>truffle migrate --reset
```

## 테스트 코드 작성

이제 테스트 코드를 작성해보자. Ganache는 미리 실행시켜놓자. 아래와 같이 test폴더에 테스트 파일을 만들자.

```shell
>cd ~/election
>touch test/election.js
```

JS파일 내부에 Truffle에서 번들로 제공하는 Mocha Testing framework와 the Chai assertion library를 활용하여 테스트 코드를 작성해보겠다. 우리가 Truffle Console에서 했던것처럼 client-side에서 조금전까지 작성한 스마트 계약을 호출할때를 시뮬레이션해보자. 테스트 코드는 아래와 같다.

```javascript
var Election = artifacts.require("./Election.sol");

contract("Election", function(accounts) {
  var electionInstance;

  it("initializes with two candidates", function() {
    return Election.deployed().then(function(instance) {
      return instance.candidatesCount();
    }).then(function(count) {
      assert.equal(count, 2);
    });
  });

  it("it initializes the candidates with the correct values", function() {
    return Election.deployed().then(function(instance) {
      electionInstance = instance;
      return electionInstance.candidates(1);
    }).then(function(candidate) {
      assert.equal(candidate[0], 1, "contains the correct id");
      assert.equal(candidate[1], "Candidate 1", "contains the correct name");
      assert.equal(candidate[2], 0, "contains the correct votes count");
      return electionInstance.candidates(2);
    }).then(function(candidate) {
      assert.equal(candidate[0], 2, "contains the correct id");
      assert.equal(candidate[1], "Candidate 2", "contains the correct name");
      assert.equal(candidate[2], 0, "contains the correct votes count");
    });
  });
});
```

위 코드를 해석해보면, 첫번째로 contract를 require로 가져와서 변수에 할당한다. 그 다음에 "contract" 함수를 호출하여 callback 함수형태로 테스트를 작성한다. 이 callback 함수는 우리 블록체인(Ganache) 모든 accounts들을 "accounts"라는 변수를 제공한다.

첫번째 테스트는 후보자 수가 2명이 맞는지 확인하는 테스트다.

두번째 테스트는 각 후보자들의 속성값들을 검증하는 테스트다.

이제 test를 실행해보자.

```shell
>cd ~/election
>truffle test
Using network 'development'.

  Contract: Election
    ✓ initializes with two candidates
    ✓ it initializes the candidates with the correct values (69ms)


  2 passing (166ms)
```

## Client-Side Application

이제 우리가 지금까지 작성한 스마트 계약과 "얘기"할 수 있는 클라이언트 앱을 만들어보자. 

기본적으로 우리가 이전 장에서 설치한 **Truffle Pet Shop box**에서 딸려온 HTML과 Javascript 파일들을 수정해서 만들 예정이다. 본 포스팅은 "이더리움 dApp" 포스팅인만큼 클라이언트 앱은 쉽고 간단하게 만들고자 한다.

"index.html"파일을 아래 코드로 변경해주자.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Election Results</title>

    <!-- Bootstrap -->
    <link href="css/bootstrap.min.css" rel="stylesheet">
  </head>
  <body>
    <div class="container" style="width: 650px;">
      <div class="row">
        <div class="col-lg-12">
          <h1 class="text-center">Election Results</h1>
          <hr/>
          <br/>
          <div id="loader">
            <p class="text-center">Loading...</p>
          </div>
          <div id="content" style="display: none;">
            <table class="table">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Name</th>
                  <th scope="col">Votes</th>
                </tr>
              </thead>
              <tbody id="candidatesResults">
              </tbody>
            </table>
            <hr/>
            <p id="accountAddress" class="text-center"></p>
          </div>
        </div>
      </div>
    </div>

    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="js/bootstrap.min.js"></script>
    <script src="js/web3.min.js"></script>
    <script src="js/truffle-contract.js"></script>
    <script src="js/app.js"></script>
  </body>
</html>
```

다음은 "app.js" 파일을 아래 코드로 변경해주자.

```javascript
App = {
  web3Provider: null,
  contracts: {},
  account: '0x0',

  init: function() {
    return App.initWeb3();
  },

  initWeb3: function() {
    if (typeof web3 !== 'undefined') {
      // If a web3 instance is already provided by Meta Mask.
      App.web3Provider = web3.currentProvider;
      web3 = new Web3(web3.currentProvider);
    } else {
      // Specify default instance if no web3 instance provided
      App.web3Provider = new Web3.providers.HttpProvider('http://localhost:7545');
      web3 = new Web3(App.web3Provider);
    }
    return App.initContract();
  },

  initContract: function() {
    $.getJSON("Election.json", function(election) {
      // Instantiate a new truffle contract from the artifact
      App.contracts.Election = TruffleContract(election);
      // Connect provider to interact with contract
      App.contracts.Election.setProvider(App.web3Provider);

      return App.render();
    });
  },

  render: function() {
    var electionInstance;
    var loader = $("#loader");
    var content = $("#content");

    loader.show();
    content.hide();

    // Load account data
    web3.eth.getCoinbase(function(err, account) {
      if (err === null) {
        App.account = account;
        $("#accountAddress").html("Your Account: " + account);
      }
    });

    // Load contract data
    App.contracts.Election.deployed().then(function(instance) {
      electionInstance = instance;
      return electionInstance.candidatesCount();
    }).then(function(candidatesCount) {
      var candidatesResults = $("#candidatesResults");
      candidatesResults.empty();

      for (var i = 1; i <= candidatesCount; i++) {
        electionInstance.candidates(i).then(function(candidate) {
          var id = candidate[0];
          var name = candidate[1];
          var voteCount = candidate[2];

          // Render candidate Result
          var candidateTemplate = "<tr><th>" + id + "</th><td>" + name + "</td><td>" + voteCount + "</td></tr>"
          candidatesResults.append(candidateTemplate);
        });
      }

      loader.hide();
      content.show();
    }).catch(function(error) {
      console.warn(error);
    });
  }
};

$(function() {
  $(window).load(function() {
    App.init();
  });
});
```

위 코드를 짧게 해석해보자면:
1) **web3 세팅**: web3.js는 클라이언트 앱에서 블록체인과 연결되기 위한 javascript 라이브러리다. 우리는 "initWeb3"라는 함수로 web3를 설정해줬다.
2) **contracts 초기화**: "initContract"라는 함수 내부에서 배포된 스마트 계약 인스턴스를 가져온다. 그리고 contracts와 소통할 수 있도록 만들어줄 몇몇 변수를 할당한다.
3) **Render 함수**: render 함수는 페이지에 스마트 계약에서 가져온 데이터들을 보여줄 수 있는 역할을 해준다. 여기서 우리는 스마트 계약에서 생성한 후보자들을 테이블에 렌더링 해주었다. 또한, 현재 블록체인에 연결되어 있는 사용자 계정을 페이지 하단에 보여주도록 하였다.

이제 클라이언트 앱을 브라우저에서 확인해보도록 하자. 그전에 우리의 contracts가 migration이 잘되어있도록 다시 한번 확인해보자

```shell
>truffle migrate --reset
```

다음으로 커맨드라인에 아래와 같이 입력하여 개발 서버를 실행하자

```shell
>npm run dev
```

자동으로 브라우저가 띄워지며 아래 그림과 같은 화면만 나올 것이다.

![브라우저 화면](http://www.dappuniversity.com/election_loading.png)

그림처럼 "Loading..." 메시지만 뜰 것이다. 이는 우리가 아직 블록체인에 로그인되어 있지 않기 때문이다. 블록체인에 연결하기 위해서는 Ganache에 있는 계정을 Metamask에 임포트해줘야한다.

## Metamask와 Ganache 연결하기

이전 장에서 설치한 Metamask는 크롬 우측상단에서 여우모양 아이콘을 클릭하면 된다.

우선 계정을 생성하라고 뜰텐데 Metamask에서 가이드하는대로 계정을 생성하면 된다.

<img width="361" alt="2019-02-22 12 18 12" src="https://user-images.githubusercontent.com/14902468/53179618-65c4ac80-3637-11e9-952a-af39deec90d4.png">

여기서 제일 하단의 '사용자 정의 RPC'를 클릭한 후, Ganache에 연결할 수 있도록 새로운 RPC URL을 설정해주자

<img width="361" alt="2019-02-22 12 19 43" src="https://user-images.githubusercontent.com/14902468/53179704-96a4e180-3637-11e9-9345-512980cc40a8.png">

새로 설정한 URL로 Metamask를 변경해주면

<img width="697" alt="2019-02-22 12 21 53" src="https://user-images.githubusercontent.com/14902468/53179874-e8e60280-3637-11e9-8d87-e92237bd4a1b.png">

이렇게 화면이 정상적으로 뜨게된다!

### 참고

[dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)
