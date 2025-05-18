// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract CheezCoin is ERC20, Ownable {
    uint256 public constant INITIAL_SUPPLY = 10_000_000 * 10**18; // 10 million tokens
    uint256 public constant TRANSFER_FEE = 5; // 5% transfer fee

    address public feeCollector;

    event Mint(address indexed to, uint256 amount);

    constructor() ERC20("CheezCoin", "CHEEZ") Ownable(msg.sender) {
        feeCollector = msg.sender;
        _mint(msg.sender, INITIAL_SUPPLY);
    }

    function transfer(address to, uint256 value) public virtual override returns (bool) {
        address owner = _msgSender();
        uint256 fee = (value * TRANSFER_FEE) / 100;
        uint256 amountAfterFee = value - fee;

        _transfer(owner, to, amountAfterFee);
        _transfer(owner, feeCollector, fee);
        return true;
    }

    function transferFrom(address from, address to, uint256 value) public virtual override returns (bool) {
        address spender = _msgSender();
        _spendAllowance(from, spender, value);

        uint256 fee = (value * TRANSFER_FEE) / 100;
        uint256 amountAfterFee = value - fee;

        _transfer(from, to, amountAfterFee);
        _transfer(from, feeCollector, fee);
        return true;
    }

    function mint(address to, uint256 amount) external onlyOwner {
        _mint(to, amount);
        emit Mint(to, amount);
    }

    function setFeeCollector(address _feeCollector) external onlyOwner {
        require(_feeCollector != address(0), "Invalid address");
        feeCollector = _feeCollector;
    }
}
