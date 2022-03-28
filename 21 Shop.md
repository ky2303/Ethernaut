Difficulty 4/10

Сan you get the item from the shop for less than the price asked?

##### Things that might help:

-   `Shop` expects to be used from a `Buyer`
-   Understanding restrictions of view functions

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

interface Buyer {
  function price() external view returns (uint);
}

contract Shop {
  uint public price = 100;
  bool public isSold;

  function buy() public {
    Buyer _buyer = Buyer(msg.sender);

    if (_buyer.price() >= price && !isSold) {
      isSold = true;
      price = _buyer.price();
    }
  }
}
```

https://docs.soliditylang.org/en/v0.6.12/contracts.html#view-functions
If the compiler’s EVM target is Byzantium or newer (default) the opcode `STATICCALL` is used when `view` functions are called, which enforces the state to stay unmodified as part of the EVM execution. For library `view` functions `DELEGATECALL` is used, because there is no combined `DELEGATECALL` and `STATICCALL`. This means library `view` functions do not have run-time checks that prevent state modifications. This should not impact security negatively because library code is usually known at compile-time and the static checker performs compile-time checks.

the vulnerability is in the fact _buyer.price() is called once for the check and another time for setting `price` 

```
if (_buyer.price() >= price && !isSold) { // price is checked here
      isSold = true;
      price = _buyer.price(); // price is set here
}
```

Tried to create a solver on remix by changing the returned price on any call after the first time _buyer.price() call

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

interface Shop {
    function buy() external;
}


contract Buyer {

  bool public called = false;
  Shop public shop;

  constructor (address shopaddress) public {
      shop = Shop(shopaddress);
  }

  function price() public returns (uint) {
      if (called == false) {
          called = true;
          return 100;
      } else {
          return 0;
      }
  }

  function callShop() public {
      shop.buy();
  }

}
```

but this failed when trying to call shop.buy()

unfortunately had to look up answer and found that the challenge has been changed since the last writeup. It used to have a gas requirement but now just checks price.
https://medium.com/@safeZ1/ethernaut-lvl-21-shop-walkthrough-how-to-use-assembly-code-in-solidity-and-abuse-solidity-d8ced86c0eb4

the assembly in this link does not work with remix so I found this writeup that calls `isSold()` in assembly as well: https://medium.com/@safeZ1/ethernaut-lvl-21-shop-walkthrough-how-to-use-assembly-code-in-solidity-and-abuse-solidity-d8ced86c0eb4

both writeups can be found on OpenZeppelin's Ethernaut forum post here: https://forum.openzeppelin.com/t/ethernaut-community-solutions/561

seems like there was an issue with this level's gas that was addressed here:
https://github.com/OpenZeppelin/ethernaut/issues/156

could not solve the challenge. tried to deploy the solution by openzeppelin but it didnt work either:
https://github.com/OpenZeppelin/ethernaut/blob/master/contracts/contracts/attacks/ShopAttack.sol
