# 5. Token

**The goal of this level is for you to hack the basic token contract below.**

You are given 20 tokens to start with and you will beat the level if you somehow manage to get your hands on any additional tokens. Preferably a very large amount of tokens.

  Things that might help:

    What is an odometer?

https://blockgeeks.com/guides/underflow-attacks-smart-contracts/
*"What this ultimately means is that the value of balances[msg.sender] – _value >= 0 will always be true regardless of the condition."*

`contract.transfer("0x1A45A9f37100bDC807c95C10dfA6faE3d83C3A39", 21)`

**END**

	Overflows are very common in solidity and must be checked for with control statements such as:

	if(a + c > a) {
	  a = a + c;
	}

	An easier alternative is to use OpenZeppelin's SafeMath library that automatically checks for overflows in all the mathematical operators. The resulting code looks like this:

	a = a.add(c);

If there is an overflow, the code will revert.
