# Car Sale Solidity Smart Contract
This smart contract for car trading enables the seller and buyer to directly trade a car without the need for an intermediary. The seller can enter the car's information into the contract and update the technical inspection status. If the buyer wishes to purchase the car, they can directly transfer the payment to the seller's account and receive the car in return.

This smart contract is particularly useful for selling second-hand cars. Additionally, it allows the seller to easily receive the payment from the buyer after the transaction is completed.

```solidity

/**
 * @title CarSaleContract
 * @dev This contract represents a car sale transaction between a seller and a buyer.
 */
 
pragma solidity ^0.8.5;

contract CarSaleContract {
    
    address payable public seller;
    address payable public buyer;
    string public carMakeModel;
    uint public carYear;
    uint public carMileage;
    uint public carPrice;
    bool public carInspectionPassed;
    bool public carSold;
    
    /**
     * @dev This event is emitted when the car is sold.
     * @param seller The address of the seller.
     * @param buyer The address of the buyer.
     * @param carPrice The price of the car in ether.
     */
    event CarSold(address indexed seller, address indexed buyer, uint indexed carPrice);
    
    /**
     * @dev Constructor function.
     * @param _seller The address of the seller.
     * @param _carMakeModel The make and model of the car.
     * @param _carYear The year of the car.
     * @param _carMileage The mileage of the car.
     * @param _carPrice The price of the car in ether.
     */
    constructor(address payable _seller, string memory _carMakeModel, uint _carYear, uint _carMileage, uint _carPrice) {
        seller = _seller;
        carMakeModel = _carMakeModel;
        carYear = _carYear;
        carMileage = _carMileage;
        carPrice = _carPrice;
    }

    /**
     * @dev Modifier to restrict access to the seller only.
     */
    modifier onlySeller() {
        require(msg.sender == seller, "Only the seller can perform this action.");
        _;
    }

    /**
     * @dev Modifier to restrict access to the buyer only.
     */
    modifier onlyBuyer() {
        require(msg.sender == buyer, "Only the buyer can perform this action.");
        _;
    }
    
    /**
     * @dev This function is called by the buyer to purchase the car.
     * It transfers the payment to the seller and sets the carSold variable to true.
     * Emits a CarSold event.
     */
    function buyCar() public payable {
        require(msg.value == carPrice, "The payment amount does not match the car price.");
        require(carInspectionPassed, "The car inspection did not pass.");
        require(!carSold, "The car has already been sold.");
        buyer = payable(msg.sender);
        seller.transfer(msg.value);
        carSold = true;
        emit CarSold(seller, buyer, carPrice);
    }
    
    /**
     * @dev This function is called by the seller to update the car inspection status.
     * @param _carInspectionPassed The new inspection status.
     */
    function updateCarInspection(bool _carInspectionPassed) public onlySeller {
        carInspectionPassed = _carInspectionPassed;
    }

    /**
     * @dev This function is called by the seller to withdraw the funds after the car is sold.
     * It transfers the funds to the seller's address.
     */
    function withdrawFunds() public onlySeller {
        require(carSold, "The car has not been sold yet.");
        seller.transfer(address(this).balance);
    }
}
```
## Variables
* seller (address payable): The address of the seller.
* buyer (address payable): The address of the buyer.
* carMakeModel (string): The make and model of the car.
* carYear (uint): The year of the car.
* carMileage (uint): The mileage of the car.
* carPrice (uint): The price of the car in ether.
* carInspectionPassed (bool): A flag to indicate if the car has passed the inspection.
* carSold (bool): A flag to indicate if the car has been sold.

## Events
* CarSold (indexed address seller, indexed address buyer, indexed uint carPrice): This event is emitted when the car is sold.

## Functions
* constructor (address payable _seller, string memory _carMakeModel, uint _carYear, uint _carMileage, uint _carPrice): This function is called when the contract is created. It sets the initial values for the seller, car make and model, year, mileage, and price.
* onlySeller (modifier): This modifier restricts access to functions that can only be called by the seller.
* onlyBuyer (modifier): This modifier restricts access to functions that can only be called by the buyer.
* buyCar (function): This function is called by the buyer to purchase the car. It transfers the payment to the seller and sets the carSold variable to true. It also emits a CarSold event.
* updateCarInspection (function): This function is called by the seller to update the car inspection status.
* withdrawFunds (function): This function is called by the seller to withdraw the funds after the car is sold. It transfers the funds to the seller's address.

## Usage
To use this smart contract, you will need to deploy it to the Ethereum blockchain using a tool like Remix or Truffle. Once the contract is deployed, you can interact with it using a web3.js enabled web application or command line tool.

To sell a car using this smart contract, follow these steps:

1. Create a new instance of the CarSaleContract contract, passing in the address of the seller, the car make and model, year, mileage, and price.
2. Have the car inspected and update the carInspectionPassed variable.
3. Wait for a buyer to call the buyCar function and send the payment in ether.
4. After the payment is received, the CarSold event will be emitted and the carSold variable will be set to true.
5. The seller can then call the withdrawFunds function to transfer the funds to their account.

Note that this smart contract is a basic example and may need to be modified to meet your specific requirements. It is important to thoroughly test your smart contract before deploying it to the blockchain to ensure that it works as expected and is secure.
