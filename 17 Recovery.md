# 17. Recovery
Difficulty 6/10

A contract creator has built a very simple token factory contract. Anyone can create new tokens with ease. After deploying the first token contract, the creator sent `0.5` ether to obtain more tokens. They have since lost the contract address.

This level will be completed if you can recover (or remove) the `0.5` ether from the lost contract address.

Can find contract being created on etherscan, with the 0.5 ether in it
![[Pasted image 20211205152019.png]]
![[Pasted image 20211205152032.png]]

SimpleToken contract has a `destroy(_to)` function that calls `selfdestruct(_to)`.  
Using https://abi.hashex.org/ to craft ABI encoded payload for `sendTransaction`

```
sendTransaction({from: "[player address]", to:"0x753d8FdF93BC25CD8015E26c2F16f9A36083B731", data:"00f55d9d000000000000000000000000[player address]"})
```

**END**

Contract addresses are deterministic and are calculated by `keccack256(address, nonce)` where the `address` is the address of the contract (or ethereum address that created the transaction) and `nonce` is the number of contracts the spawning contract has created (or the transaction nonce, for regular transactions).

Because of this, one can send ether to a pre-determined address (which has no private key) and later create a contract at that address which recovers the ether. This is a non-intuitive and somewhat secretive way to (dangerously) store ether without holding a private key.

An interesting [blog post](http://martin.swende.se/blog/Ethereum_quirks_and_vulns.html) by Martin Swende details potential use cases of this.

If you're going to implement this technique, make sure you don't miss the nonce, or your funds will be lost forever.