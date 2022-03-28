# 6. Delegation
The goal of this level is for you to claim ownership of the instance you are given.

  Things that might help

    Look into Solidity's documentation on the delegatecall low level function, how it works, how it can be used to delegate operations to on-chain libraries, and what implications it has on execution scope.
    Fallback methods
    Method ids

https://medium.com/loom-network/how-to-secure-your-smart-contracts-6-solidity-vulnerabilities-and-how-to-avoid-them-part-1-c33048d4d17d
*"The vulnerable contract’s function implemented `delegatecall` and a function from another contract that could modify ownership was left public. That allowed an attacker to craft the `msg.data` field to call the vulnerable function.

As for what would be included in the `msg.data` field, that is the signature of the function that you want to call. Signature here means the first 8 bytes of the `sha3 (alias for keccak256)`hash of the function prototype."*


`sendTransaction({from: "[player address]", to: "0xd62F6C6D08D160B176Cf6ce8DC68B5eC29bFD798" , data:"0xdd365b8b"})`

**END**
Usage of delegatecall is particularly risky and has been used as an attack vector on multiple historic hacks. With it, your contract is practically saying "here, -other contract- or -other library-, do whatever you want with my state". Delegates have complete access to your contract's state. The delegatecall function is a powerful feature, but a dangerous one, and must be used with extreme care.

Please refer to the The Parity Wallet Hack Explained article for an accurate explanation of how this idea was used to steal 30M USD.