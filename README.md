

    EIP: <to be assigned>
    Title: MoneyTalks - Standard for Smart [contract based] Assets/DigitalGoods
    Author: maxxflyer
    Type: Standard Track
    Category: ERC 
    Status: Draft
    Created: 2017-09-05 


## Simple Summary
A standard way to make your smart contract Borrowable/Rentable/Lendable as a real asset.
A unified Standard for Digital Smart Goods based on smart contracts.

The universe of **Ethereum Assets** orbits around 2 main standards.

The first is the well known ERC20, important because not only it recognizes a **token as an asset** you can own and transfer, but also because it admits [ *approve(address _spender, uint256 _value)* ] the existence of an external ecosystem of tools meant to manipulate or be manipulated by those tokens.

The second standard are the "so far forgotten" smart contracts, and is the one introduced by this ERC: *MoneyTalks*. According to this standard, any smartcontract with "value" can be considered as a Smart Asset (a Digital Good).
A Standard Smart Asset (a contract) must satisfy 3 properties:

    1 Admit Ownership
    2 Admit Ownership transfer
    3 Admit Manipulation by a third party smart environment (superOwner)

Implementing these properties,  a contract mirrors the properties of an erc20 token and can be considered a **Standard Smart Asset**.

This standard completes the economy of Ethereum and the vision of a world orbiting around contracts (in the form of assets), as a complementary logic to a previous world only based on ERC20 Assets.

## Abstract
Smart Contracts can have a value (example: a slot-machine with many historical players), therefore you may want to define them as Digital Goods, written in a way where any external Standard Module can manipulate them in term of ownership, making possible to build a whole economy based on **Smart Assets**. Smart Assets will be easily Rentable/Lendable/Autctionable by means of third party standard modules/contracts.

**The first principle is to make possible a "temporary ownership" or any other similar application.**

**The second principle is to split the rights of the Owner from the rights of a possible external "escrow system" (SuperOwner), keeping them at two different levels.**

In few words: if you recognize the existence of a SuperOwner, you admit the creation of third party standard modules/contracts for the manipulation of that specific **Standard Smart Asset**.
In the future your contract may end up in a lending action, it may be tokenized by this or that contract, it may become the object of an auction. A standard approach in the definition of the contract as Smart Asset, will keep open all the possible future destinations of your contract. A contract that is not meant to be traded or to increase its value shouldn't be declared as Smart Asset.

**According to the phylosophy of this ERC, the first digital good/asset in the Ethereum world is the smart contract, not the token**. Tokenization can be a secondary standard step one would want to apply to a Standard Smart Asset. Remeber that tokens are number and can't manipulate each others. Smart Assets are smart cotnracts, they are smart and can manipulate anything in the environment. Ideally they can manipulate theirself.


## Motivation

    In the first age Ethereum implements Token Assets. (ERC20)
Tokens are passive quantities and have no real inner function.

    In the second age Ethereum implements Smart Assets (ERC MoneyTalks).
Contracts are passive, but they are "qualities based" and can talk and also "manipulate each others". Contracts are the Assets/Digital-Goods of the future.

    In the third age (purely theoretical sci-fi) Smart Assets become active entities.
Quantity based assets may become obsolete, and may be completeley replaced by "qualities based assets", and their ACTIVE ecosystem. (Pure speculation)


## Specification
Any contract implements the variable "owner".
This standard introduces the concept of "SuperOwner": a super-entity involved in the manipulation of the ownership of an asset.

### Variables and Methods.

### owner
Any smart asset has an owner.

    address public owner;


### superowner
Any Smart Asset must allow to be manipulated by a SuperOwner, the SuperOwner can transfer the ownership of an asset to a new owner, and back.

    address public superowner;
    
    
### transferOwnership
Any smart Asset must be possibly transferred to a new owner
(if the "SuperOwner" is set, then only the SuperOwner can overwrite the Owner)

    function transferOwnership(address newOwner) onlySuper returns(bool){}


### setSuperOwner
If the "SuperOwner" is set, then only the SuperOwner can overwrite the SuperOwner.

    function setSuperOwner(address newSuperOwner) onlySuper returns(bool){}
    

### Events

### NewSuperOwner

    event NewSuperOwner(address super);
    
---

## Rationale
### Example 1: Renting out a contract.
I have my valuable slot-machine. I want to rent it out. I find a Standard Rental module, and I register it as SuperOwner of my slot-machine. You pay and become the Owner. You can't transfer the ownership, only the SuperOwner can do it. During one week you can trigger any of the function of the slot-machine (you are the owner). After one week the Rental Module brings back the ownership to me. Removes itself as SuperOwner (it only can do it!) and selfdestroys. [without a superowner the ownership would never come back, and the owner can change the fees of the slot-machine and get the income form players for one week. Let's say that we make things "universally escrowable"]

### Example 2: Creating tokenized baskets of slotmachines
Bancor introduced a nice idea: Fill a basket with various ERC20 and tokenize the basket into a unique token.
This ERC introduces the idea of filling the basket with various slot-machines (or any other Smart Asset), and later tokenize the basket into a distributable token.
The tokenization may be temporary. Ideally you can tokenize any Smart Asset, if you define it in a standard way, so that any developer can build a standard tokenizer, and at the same time the OWNERSHIP is managed at a different level, according to the specific needs.
A basket could be tokenized, or could simply be itself a Standard Smart Asset (this ERC), and be furtherly manipulated by further super-manipulators.

### Example 3: exchange ring
We have 10 slot-machines and 10 friends connected into an exchange ring. Each friend owns a slot-machine for one day. Everyday the ownerships of the slot machine rotate around the ring, so each friend changes machine daily. The Superowner is needed to do the job, while keeping ownership rights (access to inner slot-machine functions) available at Owners level.

### Example 4: Lending process
You want to borrow money and you have your valuable slot-machine. At the same time you want to keep control over the slot-machine, and continue earning money from players. A LendingContract will super-own the slot-machine, while you continue beeing the owner (with related income). A third party guy lends you money, if after a given time you don't send back the amount+%, then the ownership of the slot-machine will be transferred to the lender. If you pay back, the LenderContract removes itself as SuperOwner from the slot-machine, and your Smart Asset is free to go back in your hands.

### Example 5: A rental paradox
We have 2 Smart Assets.
They have a value because they are Rental Contracts, and they are renting out a Smart Asset.
Contract A is renting out Contract B.
Contract B is renting out Contract A.


## Implementation


    contract SmartAsset{

        address public owner;
        address public superowner;

        event NewSuperOwner(address super);

        function SmartAsset() {
            owner = msg.sender;
        }
    

        modifier onlySuper { //if superowner is not set, owner is super
            if(superowner!=address(0)){
            if (msg.sender != superowner) throw;
            }else{
            if (msg.sender != owner) throw;
            }
            _;
        }
    
        modifier onlyOwner {
            if (msg.sender != owner) throw;
            _;
        }

        //you can transfer ownership only if NO superowner is declared
        function transferOwnership(address newOwner) onlySuper returns(bool){
            owner = newOwner;
            return true;
        }
    
        //you can set a superowner only if NO superowner is already declared
        function setSuperOwner(address newSuperOwner) onlySuper returns(bool){
            superowner = newSuperOwner;    //can be an address or 0x0 (if 0x0 is declared wrong you loose control over the contract)
            NewSuperOwner(newSuperOwner);
            return true;
        }
    
        //a SAFE function to remove the superowner
        function safeReset() onlySuper returns(bool){
            superowner = address(0);
            NewSuperOwner(owner);
            return true;
        }
    

    }

    contract MyContract is SmartAsset{
        uint public x;
        function set(uint u) onlyOwner{x=u;}

        //now you can attach any kind of standard Rent/Borrow Module to your contract, or use it as an asset in a lending process,
        //meanwhile the ownership of the contract will be in the right hands, according to the needs.

    }


    

## Copyright
free
