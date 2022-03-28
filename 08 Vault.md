# 8. Vault

Unlock the vault to pass the level!

- go to etherscan for contract address:
- go to transaction details
- State tab
- Storage > Hex = 0x412076657279207374726f6e67207365637265742070617373776f7264203a29
	- as Text: A very strong secret password :)
- `contract.unlock("0x412076657279207374726f6e67207365637265742070617373776f7264203a29")`

![[Pasted image 20211104153756.png]]

**END**
Level completed!

Difficulty 3/10

It's important to remember that marking a variable as private only prevents other contracts from accessing it. State variables marked as private and local variables are still publicly accessible.

To ensure that data is private, it needs to be encrypted before being put onto the blockchain. In this scenario, the decryption key should never be sent on-chain, as it will then be visible to anyone who looks for it. zk-SNARKs provide a way to determine whether someone possesses a secret parameter, without ever having to reveal the parameter.
