# 2. Fallout
**claim ownership of the contract**
```
/* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }
```
- just call this function to become owner
