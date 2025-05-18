// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract CheezStaking is Ownable {
    IERC20 public stakingToken;
    uint256 public rewardRate;
    uint256 public totalStaked;

    struct Stake {
        uint256 amount;
        uint256 timestamp;
        uint256 accumulatedReward;
    }

    mapping(address => Stake) public stakes;

    event Staked(address indexed user, uint256 amount);
    event Unstaked(address indexed user, uint256 amount, uint256 reward);
    event RewardDeposited(uint256 amount);

    constructor(address _stakeToken) Ownable(msg.sender) {
        stakingToken = IERC20(_stakeToken);
        rewardRate = 3472222222222; // 수정: 일일 30% 이자율 (3.472e12)
    }

    function stake(uint256 amount) external {
        require(amount > 0, "Amount must be greater than 0");
        updateReward(msg.sender);
        stakingToken.transferFrom(msg.sender, address(this), amount);
        stakes[msg.sender].amount += amount;
        stakes[msg.sender].timestamp = block.timestamp;
        totalStaked += amount;
        emit Staked(msg.sender, amount);
    }

    function unstake() external {
        Stake storage userStake = stakes[msg.sender];
        require(userStake.amount > 0, "Nothing to unstake");
        updateReward(msg.sender);
        uint256 reward = userStake.accumulatedReward;
        uint256 stakedAmount = userStake.amount;
        userStake.amount = 0;
        userStake.timestamp = 0;
        userStake.accumulatedReward = 0;
        totalStaked -= stakedAmount;
        stakingToken.transfer(msg.sender, stakedAmount + reward);
        emit Unstaked(msg.sender, stakedAmount, reward);
    }

    function updateReward(address user) internal {
        Stake storage userStake = stakes[user];
        if (userStake.amount > 0) {
            uint256 timeElapsed = block.timestamp - userStake.timestamp;
            uint256 reward = (userStake.amount * rewardRate * timeElapsed) / 1e18;
            userStake.accumulatedReward += reward;
            userStake.timestamp = block.timestamp;
        }
    }

    function getReward(address user) external view returns (uint256) {
        Stake memory userStake = stakes[user];
        if (userStake.amount == 0) return 0;
        uint256 timeElapsed = block.timestamp - userStake.timestamp;
        uint256 reward = (userStake.amount * rewardRate * timeElapsed) / 1e18;
        return userStake.accumulatedReward + reward;
    }

    function depositReward(uint256 amount) external onlyOwner {
        stakingToken.transferFrom(msg.sender, address(this), amount);
        emit RewardDeposited(amount);
    }

    function setRewardRate(uint256 _rewardRate) external onlyOwner {
        rewardRate = _rewardRate;
    }
}
