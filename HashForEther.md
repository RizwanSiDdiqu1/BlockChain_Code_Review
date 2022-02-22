Code Explain
: The Below Code will give ether if the address of caller is 8
hex characters are 0 then all the ether will send to the caller.
--------------------------------------------

```solidity

1 contract HashForEther {
2
3 function withdrawWinnings() {
4 // Winner if the last 8 hex characters of the address are 0
5 require(uint32(msg.sender) == 0);
6 _sendWinnings();
7 }
8
9 function _sendWinnings() {
10 msg.sender.transfer(this.balance);
11 }
12 }

```

### Vulnerability : 

The Vulnerabiliy in this code is function visibility because by default visibility of function is public and **_sendWinnings()** does not specfiy the visibility so anyone can call the **_sendWinnings()** function can get all the ether.
