예를 들어 1000억짜리의 건물을 사려고하자.
혼자서는 이 큰 금액의 물건을 살 수 없으므로, 사람들과 돈을 모아서 사기로 했다.
이렇게 상품을 여러명이 쪼개서 사는 것을 LEITs 상품이라고한다. 

사람들은 살 대상이 되는 건물을 증권화하고, 주식회사를 세워 빌딩의 주인을 법인으로 하였다. 
그리고 사람들은 자신이 지불은 돈의 일부만큼 빌딩을 간접적으로 소유할 수 있게된다. 

이 과정을 컨트렉트로 개발을 해보려고한다. 
순서는 다음과 같다. 

1. NFT 만들기
2. ERC20 coin 만들기
3. NFT 증권화 
4. 증권화 된 NFT를 share 받기

![](https://images.velog.io/images/nnoshel/post/40bf1540-15c1-46ee-a01b-ae17bb53c129/image.png)

위 그림을 보자 다니엘은 본인의 NTF를 증권화 시키기위해
fractional.sol(=erc20 컨트렉트) 이라는 스마트 컨트렉트에 락킹을 시켜놓았다.
NFT는 ERC721 이기 때문에 이것을 ERC20으로 감싸서 하나의 코인으로 만들었다고 생각하면 쉽다. 

코인처럼 취급된 NFT를 존이라는 사람이 구매하기 위해 일정 DAI를 주고 샀다면, 그 지분만큼의 NFT를 쉐어받게 된다. 
_____

### 1. NFT 만들기

openzeppline에서 ERC721의 라이브러리를 임포트해서 사용했다. 

시작하기에 앞서 [테스트 토큰](https://faucets.chain.link/rinkeby)을 먼저 발행해야한다. 
~~~java
pragma solidity ^0.8.7;

//OpenZeppelin의 ERC721 라이브러리 임포트
import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol';

// 라이브러리 상속
contract NFT is ERC721 {

    // 생성자
    // name과 symbol를 넣으면 ERC721 NFT가 생성
    constructor(string memory name, string memory symbol) ERC721 (name, symbol){
        
    }


// 지갑주소와 토큰 아이디를 넣어주면 해당 토큰이 주소로 들어옴 
    function mint(address to, uint tokenId) external {
        _mint(to, tokenId);
    }
}
~~~


![](https://images.velog.io/images/nnoshel/post/2a3d574b-9935-442e-8e17-be7998ac15c1/image.png)
코드 작성이 완료되면 위와 같이 설정한 후 디플로이를 한다. 
Injected Web3를 선택하면 테스트넷에 들어있는 내 지갑이 불러져오게된다. 
디플로이를 하기 전에 name과 symbol을 적어주었는데 나는 HIP, HIP으로 적어주었다. 

배포가 완료되면 메타마스크 창이 뜰것이다. 
확인을 누르고 잠시 기다리면 리믹스에서 배포퇸 컨트렉트가 추가된 것을 확인 할 수 있다. 
![](https://images.velog.io/images/nnoshel/post/be4ce885-5325-4752-bbb0-63f5664e896f/image.png)

민트를 해보자.
지갑에 들어가서 복사한 주소를 입력하면 다음과 같이 HIP이라는 심볼이 자동으로 나온다.
이 토큰을 먼저 추가하고 민트를 진행해보자.
![](https://images.velog.io/images/nnoshel/post/4398b106-ce42-4393-89a7-823563db3e91/image.png)

deploy 된 컨트렉트에 mint라는 버튼이있다.
내 지갑의 주소와, 토큰아이디 1을 적었다. 
![](https://images.velog.io/images/nnoshel/post/9c39b852-d9ec-47e1-a387-bd1aa1adfca4/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-01%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%203.55.58.png)
민팅을 진행하면 아래와같이 메타마스크에서 민팅을 하겠냐고 물어본다.
![](https://images.velog.io/images/nnoshel/post/d510c6c4-f3bb-4d9f-b45b-1c0d6afcc216/image.png)

민팅이 성공했으므로 ![](https://images.velog.io/images/nnoshel/post/d00b6796-ca01-4a50-ba37-b7f6552947c0/image.png) 위와 같이 한개의 NFT를 가지고 있는 것을 확인 할 수있다. 


내 주소를 복사하고 [이더이룸 테스트넷](https://rinkeby.etherscan.io/)에 들어가자.
![](https://images.velog.io/images/nnoshel/post/417c4d20-f473-4ca2-bb9d-47eeb8dd1ae8/image.png)
다음과 같이 토큰아이디 1번을 확인 할 수 있다. 

이렇게 NFT를 만들고 민팅할 수 있다. 

____

### 2. ERC20 coin 만들기
~~~java
pragma solidity ^0.8.7;

//오픈 재플린의 ERC20라이브러리 임포트
import 'https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol';

contract GAEBI is ERC20 {

    // GAEBI 토큰발행
    constructor() ERC20('GAEBI', 'GAEBI') {}

    function mint(address to, uint amount) external {
        _mint(to, amount);
    }
}
~~~
NFT 토큰을 디플로이할 것과 같은 방법으로 디플로이를 하자.
![](https://images.velog.io/images/nnoshel/post/5e0eda72-df7f-4b91-a4f5-b36857434b5c/image.png)

디플로이를하면 이더스캔에서 다음과 같이 스마트 컨트렉트 배포에 관한 로그가 쌓여있는 것을 볼 수 있다. 
![](https://images.velog.io/images/nnoshel/post/e600121c-c27b-4446-b052-4e47030cc940/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-01%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%204.11.36.png)

토큰을 내 지갑으로 민팅해보자
![](https://images.velog.io/images/nnoshel/post/3db3a93e-a538-4a2d-8bee-0ec349faba05/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-01%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%204.19.01.png)
지갑주소를 적고 100개의 토큰을 전송하기위해 10의 18승만큼의 amount를 지정했다. 

컨트렉트의 주소를 지갑에 임포트를 하면 다음과같이 GAEBI 토큰의 정보가 뜬다. 
![](https://images.velog.io/images/nnoshel/post/2f66c74a-5ffe-44ab-9349-60609e533a85/image.png)
![](https://images.velog.io/images/nnoshel/post/7a4812ee-54f6-458d-8286-e0bf7bea92c0/image.png)
100개의 토큰이 지갑에 들어온 것을 확인 할 수 있다. 

_____
### 3. NFT 증권화 
NFT 를 쪼개서 Fungible한 형태로 받아 갈 수 있도록 하기 위함이다. 
Fungible한 형태 즉, erc20을 상속받아야한다. 
~~~java

~~~
여기까지 디플로이를 해보자
_NftAddress, 와 _NFTTokenId는 위에서 민팅한 NFT의 주소와 토큰 아이디 1을 적었다. 
NFT를 100개로 쪼개기 위해 $10^18$을 totalSupply로 적어주었고 쉐어는 1개만 받기로 하였다. 
_GAEBIADDRESS는 발행한 GAEBI Stable coin주소를 넣어주자.
![](https://images.velog.io/images/nnoshel/post/a676bef9-c654-4751-bcac-62e0a9a9f45e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-03-01%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%204.55.26.png)

위에서 디플로이가 정상적으로 잘 되었다면 Deployed Contracts에 Fractainal 컨트렉트가 생성되었을 것이다. 
이제 stake를 해보자.
![](https://images.velog.io/images/nnoshel/post/95c6d7ce-e0bb-434c-b12e-95af3f8780bf/image.png)
stake 버튼을 누르면 NFT TokenId 1이 넘어가게 될 것이다. 

![](https://images.velog.io/images/nnoshel/post/7aa7a46d-1be0-4809-aa89-4bd352fb9703/image.png)
위 에러가 난 경우 approve를 설정해 주지 않았기 때문이다. 

approve는 특정코인의 전송 권한에 대한 설정이다. NFT를 Fractainal로 옮기기 위해선 approve에서 권한을 부여해 주어야한다. 

NFT 컨트렉트에서 approve에 Fractainal 컨트렉트 주소와 NFT Tokend Id 1 을 적어준다. 
![](https://images.velog.io/images/nnoshel/post/cd0e012d-975c-4ef4-ae26-b7df330c8f1b/image.png)
![권한부여](https://images.velog.io/images/nnoshel/post/43a58c7d-758b-4855-8b9b-5b38c30d8a37/image.png)

권한 부여을 해주고 stake를 다시할 수 있다. 
![](https://images.velog.io/images/nnoshel/post/e32de4cf-d9b6-43dd-bf02-52d7e42c6d34/image.png)

![](https://images.velog.io/images/nnoshel/post/618eeb0a-564e-4570-be5e-ff05ffd06a84/image.png)

토큰을 옮길때는 항상 권한을 approve해주어야한다. 


