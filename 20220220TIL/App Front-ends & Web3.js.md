### Web3.js

Web3.js 는 이더리움에서 만든 자바스크립트 라이브러리이다. 

이더리움 네트원크는 노드로 구성되어있고, 각 노드는 블록체인의 복사본을 가지고있다. 
스마트 컨트랙트의 함수를 실행시키려면 노드에게 다음과 같은 내용을 전달해야한다. 

> 1. 스마트 컨트랙트의 주소
2. 실행하고자 하는 함수

이더리움 노드는 JSON-PRC 라고 불리는 언어로만 소통할 수 있다. 
하지만 이 언어는 다음과 같이 읽기 매우 불편하게 생겼다. 
```json
{"jsonrpc":"2.0","method":"eth_sendTransaction","params":[{"from":"0xb60e8dd61c5d32be8058bb8eb970870f07233155","to":"0xd46e8dd67c5d32be8058bb8eb970870f07244567","gas":"0x76c0","gasPrice":"0x9184e72a000","value":"0x9184e72a","data":"0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675"}],"id":1}
```
Web3.js 는 사용자의 질의를 편리하게 자바스크립트로 상호 작용할 수 있게 도와주는 수단이다. 

Web3.js 는 다음과 같이 원하는 패키지 도구로 설치 할 수 있다. 
```bash
// NPM을 사용할 때
npm install web3

// Yarn을 사용할 때
yarn add web3

// Bower를 사용할 때
bower install web3

// ...기타 등등.

```

혹은 .js 를 내려받아 프로젝트에 직접 포함시킬수도 있다.
```html
<script language="javascript" type="text/javascript" src="web3.min.js"></script>
```

______
### provider

이더리움은 같은 데이터릐 복사본으로 공유하는 노드들로 구성되어있다.
web3.js provider 를 설정하는 것은 읽기와 쓰기를 처리하면서 어떤 노드와 통신하면 좋을 지 설정을 하는 것이다. 

즉, 웹 앱에서 API 호출을 하기 위해 원격 웹 서버의 URL을 설정하는 것과 같다고 생각하면 된다. 

### Infura
infura는 빠른 읽기를 하기위해서 캐시 계층을 포함하는 다수의 이더리움 노드를 운영하는 서비스이다. 
infura를 사용하면 이더리움 블록체인과 메세지를 주고받을 수 있다. 

~~~js
var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));
~~~

위와 같이 설정하면 Web3에 프로바이더를 infura로 사용할 수 있다. 

사용자들이 블록체인에 무엇인가를 쓰려고 할 때는 어떻게 해야할까?
즉 개인키로 트렌젝션에 서명을 하려고 할 때를 생각해야한다. 

>블록체인은 트랜젝션에 전자서명을 하기위해 공개/개인 키 쌍을 이용한다. 
블록체인 데이터를 누군가 변경하면 공개키를 통해 서명을 한 사람이 누구인지 알 수 있다. 
그리고 개인키를 통해 본인의 트랜젝션을 위조하지 못하도록 방지한다. 

위와 같은 암호화 과정은 매우 복잡하므로 키를 관리 해주는 서비스들이 있다. 

### 메타마스크(Metamask)

메타마스크는 사용자들이 이더리움 계정과 개인 키를 안전하게 관리 할 수 있도록 도와주는 크롬과, 파이어폭스의 확장 프로그램이다. 
그리고 해당 계정들이 Web3.js 를 사용하는 웹 사이트들과 상호 작용을 할 수 있도록 도와준다. 

메타마스크는 web3라는 전역 자바스크립트 객체를 통해 브라우저에 web3 프로바이더를 주입한다. 
즉 브라우저에 web3가 존재하는 것을 확인하면  `web3.currentProvider` 를 프로바이더로서 사용하면 되는 것이다. 
~~~js
window.addEventListener('load', function() {

  // Web3가 브라우저에 주입되었는지 확인(MetaMask)
  if (typeof web3 !== 'undefined') {
    
    // Mist/MetaMask의 프로바이더 사용
    web3js = new Web3(web3.currentProvider);
    
  } else {
    // 사용자가 Metamask를 설치하지 않은 경우에 대해 처리
    // 사용자들에게 Metamask를 설치하라는 등의 메세지를 보여줄 것
  }

  // web3에 자유롭게 접근
  startApp()

})
~~~

위 코드는 사용자가 브라우저에 메타마스크를 설치했는지 확인하는 코드이다. 

_______
### 컨트랙트

메타마스크의 Web3 프로바이더로 web3.js를 초기화했다면, 스마트 컨트랙트와 통신을 해야한다. 

스마트 컨트렉트와 통신을 하기위해서는 **컨트랙트의 주소**와  **ABI(Application Binary Interface)**가 필요하다. 

컨트렉트가 배포되었다면 컨트렉트의 주소를 얻을 수 있다. 

ABI는 JSON 형태로 컨트랙트의 메소드를 표현하는 것으로, 배포를 위해 컨트렉트를 컴파일할 때 컴파일러가 ABI를 제공한다. 
이 제공받은** ABI는 web3.js에게 어떤 형태로 함수를 호출 해야할지 알려주는 역할을 한다. 
**
**cryptozombies_abi.js**
```js
var cryptoZombiesABI = [
  {
    "constant": false,
    "inputs": [
      {
        "name": "_to",
        "type": "address"
      },
      {
        "name": "_tokenId",
        "type": "uint256"
      }
    ],
    "name": "approve",
    "outputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [
      {
        "name": "_zombieId",
        "type": "uint256"
      }
    ],
    "name": "levelUp",
    "outputs": [],
    "payable": true,
    "stateMutability": "payable",
    "type": "function"
  },
  ...
  ```

컨트렉트 주소와, ABI를 얻으면 다음고 같이 Web3에서 인스턴스화 할 수 있다. 
~~~ js
var myContract = new web3js.eth.Contract(myABI, myContractAddress);
~~~

위 내용을 따라서 인덱스를 만들어보았다. 

~~~html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    
    <!-- abi 설정 추가 -->
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>

    <script>
      var cryptoZombies;

      function startApp() {
      // 크립토 키티의 컨트렉트 주소
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
      // 인스턴스화 
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);
      }

      window.addEventListener('load', function() {

        // Web3가 브라우저에 주입되었는지 확인(Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Mist/MetaMask의 프로바이더 사용
          web3js = new Web3(web3.currentProvider);
        } else {
          // 사용자가 Metamask를 설치하지 않은 경우에 대해 처리
          // 사용자들에게 Metamask를 설치하라는 등의 메세지를 보여줄 것
        }

        // web3에 자유롭게 접근
        startApp()

      })
    </script>
  </body>
</html>
~~~

_____

컨트렉트 설정이 모두 끝나면 Web3.js 컨트렉트와 통신 할 수 있다. 

통신을 위해서 호출해야하는 함수는 두가지가 있다. 

### Call
`call` 은 `view`와 `pure` 함수를 위해 사용한다. 
로컬 노드에서만 실행하고 블록체인엔 트랜젝션을 만들지 않는다. 
 
	`view`와 `pure` 함수는 읽기 전용이고 블록체인에서 상태를 변경하지 않음. 가스를 전혀 소모하지 않고, 메타마스크에서 트랜잭션에 서명하라고 사용자에게 창을 띄우지도 않음.

### Send
`send` 는 트랜젝션을 만들고 블록체인 상의 데이터를 변경한다. 
`view` 와 `pure`가 아닌 모든 함수에 대해서는 모두 `send`를 사용해야한다. 

좀비 데이터를 받기 위해서 `call` 을 사용해보겠다.
솔리디티에서는 `public` 으로 선언하면 자동을 게터 함수를 만들어내므로 아이디가 15인 좀비를 찾고자하면 `zombies(15)` 를 통해 받아올 수 있다. 

그럼 프론트엔드에서 좀비 아이디를 받아서 컨트렉트상에서 결과를 반환하려면 어떻게 해야할까?

~~~js
function getZombieDetails(id) {
  // 프로바이더와 통신하여 컨트렉트의 아이디가 id인 좀비를 반환
  return cryptoZombies.methods.zombies(id).call()
}

// 함수를 호출하고 결과를 가지고 무언가를 처리
getZombieDetails(15).then(function(result) {
  console.log("Zombie 15: " + JSON.stringify(result));
});
~~~
`cryptoZombies.methods.zombies(id).call()` 를 통해 원하는 아이디의 좀비를 얻어온다. 
이는 비동기적으로 일어난다. 즉 Web3는 `Promiss` 를 반환한다. 

`Promiss` 가 이행이 되면 then 문장을 실행하고, 결과를 콘솔에 기록한다. 
~~~json
{
  "name": "H4XF13LD MORRIS'S COOLER OLDER BROTHER",
  "dna": "1337133713371337",
  "level": "9999",
  "readyTime": "1522498671",
  "winCount": "999999999",
  "lossCount": "0" // Obviously.
}
~~~

____ 
### 메타마스크에서 사용자 계정 가져오기

프론트에서 사용자가 소유한 모든 좀비를 한꺼번에 가져와보자.

위에서 설명한대로 call을 사용해서 가져올 수 있지만, 인자로 owner의 주소가 필요하다. 
즉 사용자의 주소를 알아야만 모든 좀비를 알 수 있다. 
```js
function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call()
      }
```

사용자의 주소를 관리 할 수 있도록 메타마스크는 활성화 된 계정을 알려주고 있다. 
~~~js
var userAccount = web3.eth.accounts[0]
~~~

사용자는 활성화된 계정을 언제든지 바꿀 수 있으므로 계정이 바뀌었을 때마다 바뀐 계정에 대한 좀비 군대를 보여주기 위해 페이지를 업데이트해야한다. 

`setInterval` 을 사용해서 다음과 같이 작성할 수 있다. 
~~~js
var accountInterval = setInterval(function() {
  
  // 계정이 바뀌었는지 확인 100밀리초마다 확인
  if (web3.eth.accounts[0] !== userAccount) {
    
    userAccount = web3.eth.accounts[0];
    // 새 계정에 대한 UI로 업데이트하기 위한 함수 호출
    updateInterface();
  }
}, 100);
~~~

____
### 트랜젝션 보내기

`send` 함수를 사용해서 컨트렉트의 데이터를 변경해보겠다. 

>
1. 트랜잭션을 전송(send)하려면 함수를 호출한 사람의 `from` 주소가 필요하다(솔리디티 코드에서는 msg.sender). 이는 DApp의 사용자가 되어야 할 것이니, 메타마스크가 나타나 그들에게 서명을 하도록 한다.  
>
>
2. 트랜잭션 전송(send)은 가스를 소모한다. 
>
>
3. 사용자가 트랜잭션 전송을 하고 난 후 실제로 블록체인에 적용될 때까지는 **상당한 지연이 발생**한다. 트랜잭션이 블록에 포함될 때까지 기다려야 하는데, 이더리움의 평균 블록 시간이 15초이다. 만약 이더리움에 보류 중인 거래가 많거나 사용자가 가스 가격을 지나치게 낮게 보낼 경우, 우리 트랜잭션이 블록에 포함되길 기다려야 하고, 이는 몇 분씩 걸릴 수 있다.
                                                         
즉 이 코드의 비동기적 특성을 다루는 로직을 생성해야한다. 

다음은 솔리디티에서 만든 좀비를 생성하는 함수이다. 

~~~js
function createRandomZombie(string _name) public {
  require(ownerZombieCount[msg.sender] == 0);
  uint randDna = _generateRandomDna(_name);
  randDna = randDna - randDna % 100;
  _createZombie(_name, randDna);
}
~~~
위 함수를 메타마스크를 통해 web3.js 에서 호출하는 방법이다. 
~~~js
function createRandomZombie(name) {
  // 시간이 꽤 걸릴 수 있으니, 트랜잭션이 보내졌다는 것을
  // 유저가 알 수 있도록 UI를 업데이트해야 함
  $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
  
  // 컨트랙트에 전송하기
  return CryptoZombies.methods.createRandomZombie(name)
  .send({ from: userAccount })
  .on("receipt", function(receipt) {
    $("#txStatus").text("Successfully created " + name + "!");
    
    // 블록체인에 트랜잭션이 반영되었으며, UI를 다시 그려야 함
    getZombiesByOwner(userAccount).then(displayZombies);
  })
  .on("error", function(error) {
    
    // 사용자들에게 트랜잭션이 실패했음을 알려주기 위한 처리
    $("#txStatus").text(error);
  });
}
~~~
                                                        
  `receipt`는 트랜잭션이 이더리움의 블록에 포함될 때, 즉 좀비가 생성되고 컨트랙트에 저장되었을 때 발생하게 된다. 
  
`error` 는 트랜잭션이 블럭에 포함되지 못했을 때, 예를 들어 사용자가 충분한 가스를 전송하지 않았을 때 발생한다.
UI를 통해 사용자에게 트랜잭션이 전송되지 않았음을 알리고, 다시 시도할 수 있도록 해야한다. 

___
### wei
`Payable` 함수는 사용자가 레벨업 할 수 있는 곳에 추가했다.
함수를 통해 이더를 보내는 법은 간단하지만, wei로 얼마를 보낼지 정해야하는 제한이 있다. 

**wei는 이더의 가장 작은 단위이다. **
하나의 이더는 10^18개의 wei이다. 
~~~js
// 이렇게 하면 1 ETH를 Wei로 바꿀 수있다.
web3js.utils.toWei("1");
~~~
** Web3.js에는 위와 같이 wei 변환 유틸리티가 있어서** 함수를 0.001 이더를 통해 좀비를 레벨업 하기위해선 다음과 같이 작성할 수 있다. 

~~~js
CryptoZombies.methods.levelUp(zombieId)
.send({ from: userAccount, value: web3js.utils.toWei("0.001") })
~~~

____
### 이벤트 구독하기

좀비가 새로 생성될 때마다 `NewZombie` 라는 이벤트를 호출되게하였다. 

프론트에서는 이 이벤트가 호출될때마다 다음과 같은 로직을 발생하게 할 수 있다. 
~~~ js
cryptoZombies.events.NewZombie()
.on("data", function(event) {
  let zombie = event.returnValues;
  // `event.returnValue` 객체에서 이 이벤트의 세 가지 반환 값에 
  console.log("새로운 좀비가 태어났습니다!", zombie.zombieId, zombie.name, zombie.dna);
}).on("error", console.error);
~~~

위 코드는 좀비가 생성될 때마다 사용자에게 좀비가 생성되었다고 알려주는 역할을 한다. 

하지만 문제점이 있다. 
현재 사용자의 좀비뿐만이 아니라 다른 사용자의 좀비가 생성되어도 모두에게 알림이 갈 것이다. 

현재 사용자가 만든 좀비에 대해서만 알림을 받고싶다면 **index**를 사용해야한다. 

이벤트를 필터링하고 사용자와 연관된 변경만을 수신하기 위해서 ERC721 컨트렉트의 `Transfer` 이벤트에 indexed 키워드가 있던것처럼 프론트엔드의 이벤트 리스너에서 이들을 필더링해야한다. 
~~~java
contract ERC721 {
  event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
  ...
  ~~~
  
  ~~~js
// `filter`를 사용해 `_to`가 `userAccount`와 같을 때만 코드를 실행
cryptoZombies.events.Transfer({ filter: { _to: userAccount } })
.on("data", function(event) {
  let data = event.returnValues;
  // 현재 사용자가 방금 좀비를 받음
  // 해당 좀비를 보여줄 수 있도록 UI를 업데이트
}).on("error", console.error);
~~~

다음은 이벤트 로그를 알아내는 코드이다. 
~~~js
cryptoZombies.getPastEvents("NewZombie", { fromBlock: 0, toBlock: "latest" })
.then(function(events) {
  // `events`는 우리가 위에서 했던 것처럼 반복 접근할 `event` 객체들의 배열.
  // 이 코드는 생성된 모든 좀비의 목록을 받을 수 있게 할 것.
});
~~~


`fromBlock`과 `toBlock` 필터들을 이용해 이벤트 로그에 대한 시간 범위를 솔리디티에 전달할 수 있다 (`"block"`은 이더리움 블록 번호).

____
전체 코드
~~~html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>CryptoZombies front-end</title>
    <script language="javascript" type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
    <script language="javascript" type="text/javascript" src="web3.min.js"></script>
    <script language="javascript" type="text/javascript" src="cryptozombies_abi.js"></script>
  </head>
  <body>
    <div id="txStatus"></div>
    <div id="zombies"></div>

    <script>
      var cryptoZombies;
      var userAccount;

      function startApp() {
        var cryptoZombiesAddress = "YOUR_CONTRACT_ADDRESS";
        cryptoZombies = new web3js.eth.Contract(cryptoZombiesABI, cryptoZombiesAddress);

        var accountInterval = setInterval(function() {
          // 계정이 바뀌었는지 확인
          if (web3.eth.accounts[0] !== userAccount) {
            userAccount = web3.eth.accounts[0];
            // 새 계정에 대한 UI로 업데이트하기 위한 함수 호출
            getZombiesByOwner(userAccount)
            .then(displayZombies);
          }
        }, 100);

        cryptoZombies.events.Transfer({ filter: { _to: userAccount } })
        .on("data", function(event) {
          let data = event.returnValues;
          getZombiesByOwner(userAccount).then(displayZombies);
        }).on("error", console.error);
        
      }

      function displayZombies(ids) {
        $("#zombies").empty();
        for (id of ids) {
          //  좀비 상세 정보를 찾아, `zombie` 객체 반환
          getZombieDetails(id)
          .then(function(zombie) {
            // HTML에 변수를 넣기 위해 ES6의 "template literal" 사용
            // 각각을 #zombies div에 붙여넣기
            $("#zombies").append(`<div class="zombie">
              <ul>
                <li>Name: ${zombie.name}</li>
                <li>DNA: ${zombie.dna}</li>
                <li>Level: ${zombie.level}</li>
                <li>Wins: ${zombie.winCount}</li>
                <li>Losses: ${zombie.lossCount}</li>
                <li>Ready Time: ${zombie.readyTime}</li>
              </ul>
            </div>`);
          });
        }
      }

      function createRandomZombie(name) {
        // 시간이 꽤 걸릴 수 있으니, 트랜잭션이 보내졌다는 것을
        // 유저가 알 수 있도록 UI를 업데이트해야 함
        $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
        // 우리 컨트랙트에 전송하기:
        return CryptoZombies.methods.createRandomZombie(name)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Successfully created " + name + "!");
          // 블록체인에 트랜잭션이 반영되었으며, UI를 다시 그려야 함
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          // 사용자들에게 트랜잭션이 실패했음을 알려주기 위한 처리
          $("#txStatus").text(error);
        });
      }

      function feedOnKitty(zombieId, kittyId) {
        $("#txStatus").text("Eating a kitty. This may take a while...");
        return CryptoZombies.methods.feedOnKitty(zombieId, kittyId)
        .send({ from: userAccount })
        .on("receipt", function(receipt) {
          $("#txStatus").text("Ate a kitty and spawned a new Zombie!");
          getZombiesByOwner(userAccount).then(displayZombies);
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }

      function levelUp(zombieId) {
        $("#txStatus").text("좀비를 레벨업하는 중...");
        return CryptoZombies.methods.levelUp(zombieId)
        .send({ from: userAccount, value: web3.utils.toWei("0.001") })
        .on("receipt", function(receipt) {
          $("#txStatus").text("압도적인 힘! 좀비가 성공적으로 레벨업했습니다.");
        })
        .on("error", function(error) {
          $("#txStatus").text(error);
        });
      }

      function getZombieDetails(id) {
        return cryptoZombies.methods.zombies(id).call()
      }

      function zombieToOwner(id) {
        return cryptoZombies.methods.zombieToOwner(id).call()
      }

      function getZombiesByOwner(owner) {
        return cryptoZombies.methods.getZombiesByOwner(owner).call()
      }

      window.addEventListener('load', function() {

        // Web3가 브라우저에 주입되었는지 확인(Mist/MetaMask)
        if (typeof web3 !== 'undefined') {
          // Mist/MetaMask의 프로바이더 사용
          web3js = new Web3(web3.currentProvider);
        } else {
          // 사용자가 Metamask를 설치하지 않은 경우에 대해 처리
          // 사용자들에게 Metamask를 설치하라는 등의 메세지를 보여줄 것
        }

        // web3에 자유롭게 접근
        startApp()

      })
    </script>
  </body>
</html>
~~~
