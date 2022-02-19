### 이더리움 상의 토큰
 
이더리움에서 토큰은 몇몇 공통규약을 따르는 스마트 컨트렉트이다. 
즉 다른 모든 토큰 컨트렉트가 사용하는 표준 함수 집합을 구현하는 것이다. 

예를 들면 `transfer(address _to, uint256 _value)`나 `balanceOf(address _owner)` 같은 함수들이다. 

내부적으로 스마트 컨트렉트는 보통 각각의 주소에 잔액이 얼마나 있는 지 확인하는 `mapping(address => uint256) balances` 와 같은 매핑을 가지고 있다.

이 토큰을 구체적으로는  **ERC20토큰** 이라고 한다. 

모든 토큰들이 똑같은 이름의 동일한 함수 집합을 공유하므로 이 토큰들은 같은 방식으로 상호작용이 가능하다. 

다시말해서 ERC20토큰과 상화작용할수 있는 애플리케이션을 하나만들면 이 앱은 다른 ERC20토큰과도 상호작용이 가능하다. 

이런방식으로 코드를 커스텀하지 않아도 토큰의 컨트렉트 주소만 끼워넣음으로서 새로운 토큰을 생성 할 수 있다. 

예를 들어, 한 거래소에서 ERC20 토큰을 상장하면 거래소는 통신이 가능한 또 스마트 컨트렉트를 추가한다.
사용자들은 이 새로 상장된 컨트랙트에게 거래소의 지갑 주소로 토큰을 보내라고 할 수 있고, 거래소는 이 컨트랙트에서 사용자들이 출금을 신청하면 토큰을 돌려보내라고 통신 할 수 있다. 

거래소는 위 전송로직을 한 번만 구현하면 된다. 그리고 동일하게 토큰을 추가하고 싶으면 단순히 새 컨트렉트 주소를 추가하는 것으로 끝난다. 

### ERC721
ERC20 토큰은 화폐처럼 사용하기에 아주 적합하다. 하지만 게임에서는 사용하기에 부적절하다. 

**1. 좀비는 화폐처럼 분할이 가능하지 않다. **
	0.234 이더를 보내는 것은 가능하지만 0.234개의 좀비를 보내는 것은 불가능하다. 
**2. 모든 좀비는 동일하지 않다. **
	Level 1 좀비와 , Level 234 좀비는 완벽히 다르다. 
  
  
**ERC721** 토큰은 좀비와 같은 수집품을 위한 표준 토큰이다. 
ERC721 토큰은 각각각의 토큰이 유일하고 분할이 불가능하므로 하나의 토큰을 전체 단위로만 거래할 수 있다.

> **ERC721과 같은 표준을 사용하면 컨트랙트에서 사용자들이 좀비를 거래/판매할 수 있도록 하는 경매나 중계 로직을 우리가 직접 구현하지 않아도 된다.**
누군가 ERC721 자산을 거래할 수 있도록 하는 거래소 플랫폼을 만들면 ERC721 좀비들을 그 플랫폼에서 쓸 수 있게 될 것이다. 그러므로 거래 로직을 만드느라 고생하는 것보다 토큰 표준을 사용하는 것이 명확한 이점이 있는 것이다.
    
  ________
    
 ### ERC721 표준, 다중 상속

다음은 ERC721 표준이다. 

> contract ERC721 {
  event Transfer(address indexed _from, address indexed _to, uint256 _tokenId);
  event Approval(address indexed _owner, address indexed _approved, uint256 _tokenId);
>
  function balanceOf(address _owner) public view returns (uint256 _balance);
  function ownerOf(uint256 _tokenId) public view returns (address _owner);
  function transfer(address _to, uint256 _tokenId) public;
  function approve(address _to, uint256 _tokenId) public;
  function takeOwnership(uint256 _tokenId) public;
}

위 메소드들이 구현해야하는 메소드들이다. 

>위 메소드 중에 `ownerOf` 라는 이름의 메소드를 보자.
>
`ownerOf` 이름의 `modifier` 가 이미 `zombiefeeding`에 구현되어있다.
그렇다고 ERC721 표준으로 되어있는 `ownerOf` 이름을 바꾸는 것은 안되므로, `modifier` 의 이름을 `onlyOwnerOf` 로 수정하고 진행하겠다. 



다음과 같이 새 컨트렉트를 만들자.

**zombieownership.sol**
```java
pragma solidity ^0.4.19;

// zombieattack, erc721 상속
import "./zombieattack.sol";
import "./erc721.sol";

contract ZombieOwnership is ZombieAttack, ERC721 {

}
```
다수의 컨트렉트를 상속할 때는  (,)를 사용해서 상속 받을 수 있다. 


> 참고: ERC721 표준은 현재 초안인 상태이고, 아직 공식으로 채택된 구현 버전은 없다. 지금은 OpenZeppelin 라이브러리에서 쓰이는 현재 버전을 사용할 것이지만, 공식 릴리즈 이전에 언젠가 바뀔 가능성도 있다.



_____

위 ERC721 표준에 있는 메소드를 구현해보자.

### balanceOf

이 함수는 단순히 `address`를 받아, 해당 `address`가 토큰을 얼마나 가지고 있는지 반환한다.
여기서 "토큰"은 좀비들이다. 

```java
function balanceOf(address _owner) public view returns (uint256 _balance) {
    return ownerZombieCount[_owner];
  }
```
### ownerOf

이 함수에서는 토큰 ID(좀비 ID)를 받아, 이를 소유하고 있는 사람의 `address`를 반환한다.

```java
function ownerOf(uint256 _tokenId) public view returns (address _owner) {
    return zombieToOwner[_tokenId];
  }
  ```
  
정보를 저장하는 mapping을 DApp에 이미 가지고 있기 때문에, 위 함수들은 단 한 줄로 구현할 수 있다.  

### transfer
ERC721 스펙에서는 토큰을 전송하기위한 두가지 방법이 있다. 

첫 번째 방법은 토큰의 소유자가 전송 상대의 `address`, 전송하고자 하는 `_tokenId`와 함께 `transfer` 함수를 호출하는 것이다. 

### approve
`approve` 는 토큰을 전송하는 또 다른 방법이다. 

`transfer` 와 동일하게 전송 상대의 `address`, 전송하고자 하는 `_tokenId` 정보를 가지고 호출하고 컨트렉트의 허가 정보를 저장한다. 

`takeOwership`은 `mapping (uint256 => address)` 써서 를`msg.sender`가 소유자로부터 토큰을 받을 수 있게 허가를 받았는지 확인하고 허가를 받은 경우에만 토큰을 전송한다.

즉, 전송 순서는 다음 처럼 두 단계로 이뤄진다.
>
1. 소유자인 자네가 새로운 소유자의 address와 그에게 보내고 싶은 _tokenId를 사용하여 approve를 호출.
>
2. 새로운 소유자가 _tokenId를 사용하여 takeOwnership 함수를 호출하면, 컨트랙트는 승인된 자인지 확인하고 토큰을 전송.

`approve` 는 토큰을 보낼 때 호출하고 `takeOwership` 는 토큰을 받을 때 호출하는 것을 제외하면 **동일한 전송로직**을 가지고있다. 

그러므로 위 두가지 로직을 처리할 `private` 함수를 **추상화**하는 것이 좋다. 

또한 두번의 함수 호출이 있으므로 함수 사이에 누가 무엇에 대해 승인이 되었는지 기록해야한다. 

```java
pragma solidity ^0.4.19;

import "./zombieattack.sol";
import "./erc721.sol";

contract ZombieOwnership is ZombieAttack, ERC721 {

// apporval이 승인된 주소를 저장하기위한 매핑
  mapping (uint => address) zombieApprovals;

  function balanceOf(address _owner) public view returns (uint256 _balance) {
    return ownerZombieCount[_owner];
  }

  function ownerOf(uint256 _tokenId) public view returns (address _owner) {
    return zombieToOwner[_tokenId];
  }

// 추상화
  function _transfer(address _from, address _to, uint256 _tokenId) private {
  	// 받는 사람의 좀비 매핑 수 증가
    ownerZombieCount[_to]++;
    
    // 받는 사람의 좀비 매핑 수 감소
    ownerZombieCount[_from]--;
    
    // 받는 사람의 좀비 매핑 수 증가
    zombieToOwner[_tokenId] = _to;
    
    // erc721.sol에 정의된 전송 이벤트 
    Transfer(_from, _to, _tokenId);
  }
  										// 좀비(토큰) 소유자만 전송할 수 있도록 제어자 추가
  function transfer(address _to, uint256 _tokenId) public onlyOwnerOf(_tokenId) {
    _transfer(msg.sender, _to, _tokenId);
  }


  function approve(address _to, uint256 _tokenId) public {
  	// 매핑에 좀비를 보낸 사람 저장	
	zombieApprovals[_tokenId] = _to;
    
    // erc721.sol에 정의된 승인 이벤트 
    Approval(msg.sender, _to, _tokenId);
  }

  function takeOwnership(uint256 _tokenId) public {
  // 좀비를 받을 사람과 함수를 호출한 사람이 같아야함
    require(zombieApprovals[_tokenId] == msg.sender);
    
    // 토큰을 보내는 사람의 주소
    address owner = ownerOf(_tokenId);
    
    // 토큰을 보내는사람, 토큰을 받을 사람 == 함수를 호출한사람, 토큰
    _transfer(owner, msg.sender, _tokenId);
  }
}
```
_____

### SafeMath

8비트 데이터를 저장할 수 있는 uint8이 저장할 수 있는 가장 큰 수는 이진수로 11111111(또는 십진수로 2^8 - 1 = 255)이다. 

다음은 일부로 오버플로우를 발생시키는 코드이다. 
>
```java
uint8 number = 255;
number++;
```

`number` 의 값은 00000000으로 즉 직관적으론 0이 된다. 
이것을 오버플로우라고 한다. 

언더플로우는 이와 반대로 0에서 1을 빼면 255가 되는 것을 말한다. (uint는 음수가 될 수 없다. )

이를 막기위해, OpenZeppelin에서 기본적으로 이런 문제를 막아주는 SafeMath라고 하는 라이브러리를 만들었다. 


그러므로 `safemath.sol` 라이브러리에는 uint256, SafeMath16과 SafeMath32라는 타입에 맞는 코드들이 구현되어있어야한다. 

이 라이브러리는 타입 외에는 구현내용이 동일하다. 

SafeMath 내부의 코드이다.

>
```java
pragma solidity ^0.4.18;
/**
 * @title SafeMath
 * @dev Math operations with safety checks that throw on error
 */
library SafeMath {
>
  /**
  * @dev Multiplies two numbers, throws on overflow.
  */
  function mul(uint256 a, uint256 b) internal pure returns (uint256) {
    if (a == 0) {
      return 0;
    }
    uint256 c = a * b;
    assert(c / a == b);
    return c;
  }
>
  /**
  * @dev Integer division of two numbers, truncating the quotient.
  */
  function div(uint256 a, uint256 b) internal pure returns (uint256) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint256 c = a / b;
    // assert(a == b * c + a % b); // There is no case in which this doesn't hold
    return c;
  }
>
  /**
  * @dev Subtracts two numbers, throws on overflow (i.e. if subtrahend is greater than minuend).
  */
  function sub(uint256 a, uint256 b) internal pure returns (uint256) {
    assert(b <= a);
    return a - b;
  }
>
  /**
  * @dev Adds two numbers, throws on overflow.
  */
  function add(uint256 a, uint256 b) internal pure returns (uint256) {
    uint256 c = a + b;
    assert(c >= a);
    return c;
  }
}
>
/**
 * @title SafeMath32
 * @dev SafeMath library implemented for uint32
 */
library SafeMath32 {
>
  function mul(uint32 a, uint32 b) internal pure returns (uint32) {
    if (a == 0) {
      return 0;
    }
    uint32 c = a * b;
    assert(c / a == b);
    return c;
  }
>
  function div(uint32 a, uint32 b) internal pure returns (uint32) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint32 c = a / b;
    // assert(a == b * c + a % b); // There is no case in which this doesn't hold
    return c;
  }
>
  function sub(uint32 a, uint32 b) internal pure returns (uint32) {
    assert(b <= a);
    return a - b;
  }
>
  function add(uint32 a, uint32 b) internal pure returns (uint32) {
    uint32 c = a + b;
    assert(c >= a);
    return c;
  }
}
>
/**
 * @title SafeMath16
 * @dev SafeMath library implemented for uint16
 */
library SafeMath16 {
>
  function mul(uint16 a, uint16 b) internal pure returns (uint16) {
    if (a == 0) {
      return 0;
    }
    uint16 c = a * b;
    assert(c / a == b);
    return c;
  }
>
  function div(uint16 a, uint16 b) internal pure returns (uint16) {
    // assert(b > 0); // Solidity automatically throws when dividing by 0
    uint16 c = a / b;
    // assert(a == b * c + a % b); // There is no case in which this doesn't hold
    return c;
  }
>
  function sub(uint16 a, uint16 b) internal pure returns (uint16) {
    assert(b <= a);
    return a - b;
  }
  >
  function add(uint16 a, uint16 b) internal pure returns (uint16) {
    uint16 c = a + b;
    assert(c >= a);
    return c;
  }
}
```

`library` 키워드는 `using` 키워드를 사용할 수 있게해준다. 
이를 통해 라이브러리의 메소드들을 아래처럼 다른 데이터 타입에 적용 시킬 수 있다. 


SafeMath 라이브러리를 쓸 때는 **using SafeMath for uint256, using SafeMath for uint32;, using SafeMath for uint16;** 이라는 구문을 사용해야한다. 
이 구문은 연산을 할 때 인자의 타입을 uint256, 32, 16으로 변환 한다는 의미이다. 

사용 방법을 보자
> ```java
using SafeMath for uint;
uint test = 2;
test = test.mul(3); // test는 6
test = test.add(5); // test는 11
```
`mul`과 `add` 함수는 각각 2개의 인수를 필요로 한다.
하지만`using SafeMath for uint`를 선언할 때, 함수를 적용하는 `uint(test)`는 **첫 번째 인수로 자동으로 전달**된다. 
>
위 `add` 함수를 보자 c 는 a와 b를 단순히 더하지만 **assert 구문을 써서 그 합이 a보다 크도록 보장하지. 이것이 오버플로우를 막아준다.**
>
`assert`는 조건을 만족하지 않으면 에러를 발생시킨다는 점에서 `require`와 비슷하지만, **`require`는 함수 실행이 실패하면 남은 가스를 사용자에게 되돌려 주고 `assert`는 그렇지 않다.**
그러므로 `assert`는 일반적으로 코드가 심각하게 잘못 실행될 때 사용한다. 
>> **SafeMath의 add, sub, mul, 그리고 div는 4개의 기본 수학 연산을 하는 함수이지만, 오버플로우나 언더플로우가 발생하면 에러를 발생시키는 역할을 한다.** 
>
#### 

위의 코드를 SafeMath를 상속 받고 연산하는 부분을 수정해보자.

```java
function _transfer(address _from, address _to, uint256 _tokenId) private {
    ownerZombieCount[_to] = ownerZombieCount[_to].add(1);
    // ownerZombieCount[_to]++;
    ownerZombieCount[msg.sender] = ownerZombieCount[msg.sender].sub(1);
   // ownerZombieCount[_from]--;
    zombieToOwner[_tokenId] = _to;
    Transfer(_from, _to, _tokenId);
  }
```
+, - 연산을 각각의 메소드를 사용해서 수정했다. 

위와 같은 방법으로 소스 내의 연산을 수정하면 된다. 

![](https://images.velog.io/images/nnoshel/post/3dc8ecd8-c7d2-41a8-b7bc-04f4b6dd7661/image.png)




