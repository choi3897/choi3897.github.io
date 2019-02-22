---
title: "Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #6.Cast Votes"
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

1. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #1.Blockchain이란](https://choi3897.github.io/ethereum/ethereum-dapp-1/#)
2. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #2.Smart Contract란](https://choi3897.github.io/ethereum/ethereum-dapp-2/#)
3. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #3.개발환경 설정](https://choi3897.github.io/ethereum/ethereum-dapp-3/#)
4. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #4.Smoke Test](https://choi3897.github.io/ethereum/ethereum-dapp-4/#)
5. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #5.List Candidates](https://choi3897.github.io/ethereum/ethereum-dapp-5/#)
6. [**Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #6. Cast Votes**](https://choi3897.github.io/ethereum/ethereum-dapp-6/#)
7. [Ethereum(이더리움) 투표 Dapp 개발 Tutorial - #7. Watch Events](https://choi3897.github.io/ethereum/ethereum-dapp-7/#)

# 투표 기능 추가하기

---

## Election.sol 수정하기

이제 우리의 선거 dApp에 투표 기능을 추가해보자. 먼저 이미 투표한 accounts들을 구분할 수 있는 "voters"이라는 이름의 mapping 변수를 선언한다.

```javascript
contract Election {
    // ...

    // Store accounts that have voted
    mapping(address => bool) public voters;

    // ...
}
```

추가로 "vote" 함수도 추가해보자.

```javascript
contract Election {
    // ...

    // Store accounts that have voted
    mapping(address => bool) public voters;

    // ...

    function vote (uint _candidateId) public {
        // require that they haven't voted before
        require(!voters[msg.sender], "This Voter has already voted!");

        // require a valid candidate
        require(_candidateId > 0 && _candidateId <= candidatesCount, "There is no such candidate");

        // record that voter has voted
        voters[msg.sender] = true;

        // update candidate vote Count
        candidates[_candidateId].voteCount ++;
    }
}
```

"vote" 함수의 핵심 기능은 "candidates" mapping 변수에서 Candidate struct를 읽어와 후보자의 투표 수를 증가시키는 것이다. 이 외에도 아래와 같은 특징이 있다:

1. "vote"함수는 unsigned integer 타입의 후보자 id를 파라미터로 받는다.
2. 외부 account에서 해당 함수를 호출하고자 하기 때문에 **public**으로 선언하였다.
3. 투표를 한 account는 위에서 우리가 선언한 voters라는 mapping변수에 추가한다. 이를 통해 이미 투표에 참여한 투표자를 걸러낼 수 있게 된다. 우리는 함수를 호출한 account를 Solidity에서 제공하는 글로벌 변수(global variable) <span style="color:#4169E1">**"msg.sender"**</span>를 통해 가져올 수 있다.
4. 본 함수는 "require" 라는 구문으로 특정 조건에 만족하지 않으면 실행을 중지시키고 있다. 첫번째 require는 투표자가 이전에 투표하지 않았음을 확인하는 부분이다. 함수를 호출한 account인 "msg.sender"를 voters mapping에서 읽어옴으로써 확인이 가능하다. 두번째 require는 파라미터로 넘어온 후보자 ID(candidateId)가 유효한지 확인하는 부분이다. 반드시 0보다 크고 전체 후보자 수보다는 같거나 작아야 한다.

완성된 contract 코드는 아래와 같을 것이다.

```javascript
pragma solidity 0.5.0;


contract Election {
    // Model a Candidate
    struct Candidate {
        uint id;
        string name;
        uint voteCount;
    }

    // Store accounts that have voted
    mapping(address => bool) public voters;
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

    function vote(uint _candidateId) public {
        // require that they haven't voted before
        require(!voters[msg.sender],"This Voter has already voted!");

        // require a valid candidate
        require(_candidateId > 0 && _candidateId <= candidatesCount, "There is no such candidate");

        // record that voter has voted
        voters[msg.sender] = true;

        // update candidatte vote Count
        candidates[_candidateId].voteCount++;
    }
}

```

## 투표 함수 Test하기

우리가 방금까지 작성한 투표 기능을 테스트하기 위해서 "election.js" 테스트 파일에 아래 코드를 추가한다.

```javascript
it("allows a voter to cast a vote", function() {
    return Election.deployed().then(function(instance) {
      electionInstance = instance;
      candidateId = 1;
      return electionInstance.vote(candidateId, { from: accounts[0] });
    }).then(function(receipt) {
      return electionInstance.voters(accounts[0]);
    }).then(function(voted) {
      assert(voted, "the voter was marked as voted");
      return electionInstance.candidates(candidateId);
    }).then(function(candidate) {
      var voteCount = candidate[2];
      assert.equal(voteCount, 1, "increments the candidate's vote count");
    })
  });
```

위 테스트 코드로 우리는 2가지를 테스트하고자 한다.

1. "vote" 함수가 후보자의 투표수를 증가시키는지 테스트
2. 투표자가 투표했을 때, voters mapping 변수에 추가되는지 테스트

다음으로 우리가 require로 Validation했던 부분을 테스트해보자.

```javascript
it("throws an exception for invalid candidates", function() {
    return Election.deployed().then(function(instance) {
      electionInstance = instance;
      return electionInstance.vote(99, { from: accounts[1] })
    }).then(assert.fail).catch(function(error) {
      assert(error.message.indexOf('revert') >= 0, "error message must contain revert");
      return electionInstance.candidates(1);
    }).then(function(candidate1) {
      var voteCount = candidate1[2];
      assert.equal(voteCount, 1, "candidate 1 did not receive any votes");
      return electionInstance.candidates(2);
    }).then(function(candidate2) {
      var voteCount = candidate2[2];
      assert.equal(voteCount, 0, "candidate 2 did not receive any votes");
    });
  });
```

다음으로는 중복 투표를 방지하는지 확인하는 테스트 코드를 작성해보자.

```javascript
it("throws an exception for double voting", function() {
    return Election.deployed().then(function(instance) {
      electionInstance = instance;
      candidateId = 2;
      electionInstance.vote(candidateId, { from: accounts[1] });
      return electionInstance.candidates(candidateId);
    }).then(function(candidate) {
      var voteCount = candidate[2];
      assert.equal(voteCount, 1, "accepts first vote");
      // Try to vote again
      return electionInstance.vote(candidateId, { from: accounts[1] });
    }).then(assert.fail).catch(function(error) {
      assert(error.message.indexOf('revert') >= 0, "error message must contain revert");
      return electionInstance.candidates(1);
    }).then(function(candidate1) {
      var voteCount = candidate1[2];
      assert.equal(voteCount, 1, "candidate 1 did not receive any votes");
      return electionInstance.candidates(2);
    }).then(function(candidate2) {
      var voteCount = candidate2[2];
      assert.equal(voteCount, 1, "candidate 2 did not receive any votes");
    });
  });
```

위 테스트 시나리오에서는 첫번째로 아직 투표하지 않은 투표자로 최초 투표를 시도한다. 그 뒤로 다시 투표를 시도해본다.

이제 여기까지 짜여진 테스트 코드로 테스트를 돌려보자!

```console
>cd ~/election
>truffle test
Using network 'development'.

Compiling ./contracts/Election.sol...


  Contract: Election
    ✓ initializes with two candidates
    ✓ it initializes the candidates with the correct values (49ms)
    ✓ allows a voter to cast a vote (102ms)
    ✓ throws an exception for invalid candidates (132ms)
    ✓ throws an exception for double voting (201ms)


  5 passing (547ms)
```

# Client에서 투표하기

## index.html 수정하기
이제 Client에서 투표를 해볼 차례다.
먼저 "index.html" 파일에서 table 요소 아래에 다음과 같은 코드를 추가한다.

```html
<form onSubmit="App.castVote(); return false;">
  <div class="form-group">
    <label for="candidatesSelect">Select Candidate</label>
    <select class="form-control" id="candidatesSelect">
    </select>
  </div>
  <button type="submit" class="btn btn-primary">Vote</button>
  <hr />
</form>
```

위 코드를 해석해보자면:

1. 빈 select 요소를 가지고 있는 form을 만든다. select 요소에는 우리 smart contract에서 제공하는 후보자들을 "app.js" 파일에서 받아와 채워넣을 것이다.
2. form의 "onSubmit" 핸들러에는 "castVote" 함수를 호출하도록 하였다. 이 또한 "app.js"에 정의할 것이다.

## app.js 수정하기

먼저 smart contract로부터 모든 후보자 리스트를 가져와 form의 select 요소에 채워넣을 것이다. 그 후, 이미 투표한 account라면 form을 숨김처리하여 중복투표를 못하게 막을 것이다. app.js의 render 함수를 아래와 같이 작성한다.

```javascript
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

    var candidatesSelect = $('#candidatesSelect');
    candidatesSelect.empty();

    for (var i = 1; i <= candidatesCount; i++) {
      electionInstance.candidates(i).then(function(candidate) {
        var id = candidate[0];
        var name = candidate[1];
        var voteCount = candidate[2];

        // Render candidate Result
        var candidateTemplate = "<tr><th>" + id + "</th><td>" + name + "</td><td>" + voteCount + "</td></tr>"
        candidatesResults.append(candidateTemplate);

        // Render candidate ballot option
        var candidateOption = "<option value='" + id + "' >" + name + "</ option>"
        candidatesSelect.append(candidateOption);
      });
    }
    return electionInstance.voters(App.account);
  }).then(function(hasVoted) {
    // Do not allow a user to vote
    if(hasVoted) {
      $('form').hide();
    }
    loader.hide();
    content.show();
  }).catch(function(error) {
    console.warn(error);
  });
}
```

form이 submit 됐을 때 호출될 castVote 함수도 작성하자

```javascript
castVote: function() {
    var candidateId = $('#candidatesSelect').val();
    App.contracts.Election.deployed().then(function(instance) {
      return instance.vote(candidateId, { from: App.account });
    }).then(function(result) {
      // Wait for votes to update
      $("#content").hide();
      $("#loader").show();
    }).catch(function(err) {
      console.error(err);
    });
  }
```

먼저 form으로부터 candidateId를 가져온 뒤, smart contract의 vote함수를 호출한다. 이때 candidateId와 함께 현재 account를 함수의 "from" 메타데이터에 넣어서 호출한다. 이 호출은 비동기로 호출이며, 함수 호출을 한 뒤 페이지 내용을 숨기고 로딩화면을 띄운다. 투표가 기록되면 반대로 페이지 내용을 다시 유저에게 보여준다.

화면을 확인하기 전에 Election.sol의 변경사항을 반영하기 전에 다시 migrate 하고 재실행해주자

```console
>cd ~/election
>truffle migrate --reset
>npm run dev
```

아래와 같은 화면이 뜬다면 성공적이다.

<img width="720" alt="2019-02-22 11 50 31" src="https://user-images.githubusercontent.com/14902468/53249946-afc39600-36fc-11e9-95ef-c6a6db12f536.png">

이제 기호1번의 후보자에게 투표해보자. Vote 버튼을 누르는 순간 아래와 같은 팝업이 나타날 것이다. (팝업이 나타나지 않으면 Metamask에 가보면 볼 수 있다)

<img width="361" alt="2019-02-22 11 53 04" src="https://user-images.githubusercontent.com/14902468/53250084-0b8e1f00-36fd-11e9-97b7-861a34fe52a9.png">

"승인" 버튼을 누르면 트랜잭션이 전송되고 투표가 완료된다.

아래처럼 트랜잭션 로그를 확인할 수 있다.

<img width="361" alt="2019-02-22 11 55 15" src="https://user-images.githubusercontent.com/14902468/53250236-55770500-36fd-11e9-8b3c-267f92f477d7.png">

하지만 투표를 완료해도 이상하게 loading 화면만 계속 떠있는 것을 확인할 수 있다.

다음 장에서는 이 현상을 해결해줄 수 있도록 투표가 기록되면 페이지를 리프레쉬해주는 기능 개발에 대해서 포스팅해보겠다.

### 참고

[dappuniversity : the-ultimate-ethereum-dapp-tutorial](http://www.dappuniversity.com/articles/the-ultimate-ethereum-dapp-tutorial)
