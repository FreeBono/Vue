# day4

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.8.8;


contract FundRaising {
    uint public constant MINIMUM_AMOUNT = 1e16; 
    uint public fundRaisingCloses;
    address public beneficiary;
    // address[] funders;
    uint[] public amount;
    mapping(address => uint256) funderToAmount;
    address[] funders;

    
    constructor (uint _duration, address _beneficiary) {
        fundRaisingCloses = block.timestamp + _duration;
        beneficiary = _beneficiary;
    }
    
    function fund() public payable {
        require(msg.value >= MINIMUM_AMOUNT, "MINIMUM_AMOUNT: 0.01 ether");
        require(block.timestamp < fundRaisingCloses, "FUND RAISING CLOSED"); 
      
        addFunder(msg.sender);
        funderToAmount[msg.sender] += msg.value;
        amount.push(msg.value);
    }
    

    function addFunder(address _funder) internal {
        if(funderToAmount[_funder] == 0 ) {
            funders.push(_funder);
        }
    }

    function currentCollection() public view returns(uint256) {
        if(address(this) == address(0)) return 0;
        return address(this).balance;      
    }
    
    modifier onlyBeneficiary() {
        require(msg.sender == beneficiary, "NOT BENEFICIARY ADDRESS");
        _;
    }

    modifier onlyAfterFundCloses {
        require(block.timestamp > fundRaisingCloses, "FUND NOT CLOSES YET");
        _;
    }

    function withdraw() public payable
    onlyBeneficiary
    onlyAfterFundCloses {
    //   require(msg.sender == beneficiary);
    //   require(block.timestamp > fundRaisingCloses);
        msg.sender.transfer(address(this).balance); 
    }
    

    function selectRandomFunder() public view returns (address, uint256) {
        if(funders.length == 0) return (address(0), 0);
        
        bytes32 rand = keccak256(abi.encodePacked(blockhash(block.number)));
        address selected = (funders.length == 1 ) ? funders[0] : funders[uint(rand) % funders.length];
        return (selected, funderToAmount[selected]);
    }

}
```

