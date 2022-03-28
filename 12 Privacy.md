# 12. Privacy
The creator of this contract was careful enough to protect the sensitive areas of its storage.

Unlock this contract to beat the level.

Things that might help:

    Understanding how storage works
    Understanding how parameter parsing works
    Understanding how casting works

Tips:

    Remember that metamask is just a commodity. Use another tool if it is presenting problems. Advanced gameplay could involve using remix, or your own web3 provider.

**casting a bytes32 to bytes16**

castTest.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract castTest {
    bytes32 public a = 0x1111111111111111111111111111111100000000000000000000000000000000;
    bytes16 public b;
    
    function cast_test() public returns (bytes16) {
        b = bytes16(a);
    }
}
```

calling cast_test(), then viewing b gives 0x1111111111111111, so casting bytes32 to bytes16 takes the first 16 bytes

- go to contract on etherscan
- go to transaction details > state tab, find contract
	- 6 storage addresses, corresponding to the 6 variables in the contract (locked, ID, flattening, denomination, awkwardness, data)  
	- data is storage address 5, we have after: 0xbb4e4e7a7c2bf1431507850592d960bf005654721c2784a006aa823786ca35ac  
	- each byte is 2 characters, this is only 64 characters
		https://docs.soliditylang.org/en/v0.8.10/internals/layout_in_storage.html
		"If a value type does not fit the remaining part of a storage slot, it is stored in the next storage slot.
		Structs and array data always start a new slot and their items are packed tightly 
		according to these rules."
		this looks like only one byte32
	- since the cast in unlock() will only take first 16, used python
```
$ python3
Python 3.8.10 (default, Sep 28 2021, 16:10:42) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> a="bb4e4e7a7c2bf1431507850592d960bf005654721c2784a006aa823786ca35ac"
>>> a[0:32]
'bb4e4e7a7c2bf1431507850592d960bf'
```
`contract.unlock("0xbb4e4e7a7c2bf1431507850592d960bf")`
```
await contract.locked()
false
```
		
**END**
Nothing in the ethereum blockchain is private. The keyword private is merely an artificial construct of the Solidity language. Web3's getStorageAt(...) can be used to read anything from storage. It can be tricky to read what you want though, since several optimization rules and techniques are used to compact the storage as much as possible.

It can't get much more complicated than what was exposed in this level. For more, check out this excellent article by "Darius": How to read Ethereum contract storage

