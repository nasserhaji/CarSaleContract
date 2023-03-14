# CarSaleContract

```solidity
/.;'\\
/**
 * @title CarSaleContract
 * @dev This contract represents a car sale transaction between a seller and a buyer.
 */
 
pragma solidity ^0.8.0;

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
