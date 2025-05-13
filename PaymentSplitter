// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

/// @title PaymentSplitter
/// @notice Allows the owner to register contributors with share percentages and split ETH payments among them.
/// @dev Ensures security via ReentrancyGuard and owner-only access control.

import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract PaymentSplitter is Ownable, ReentrancyGuard {
    struct Contributor {
        uint256 share;    // Share percentage (e.g., 25 for 25%)
        uint256 released; // Total ETH already withdrawn
    }

    mapping(address => Contributor) public contributors;
    address[] public contributorList;

    uint256 public totalShares;
    uint256 public totalReceived;
    uint256 public totalReleased;

    event ContributorRegistered(address indexed wallet, uint256 share);
    event PaymentReceived(address indexed from, uint256 amount);
    event PaymentWithdrawn(address indexed to, uint256 amount);

    /// @notice Constructor sets the initial owner of the contract
    /// @param initialOwner addresses the owner of the contract
    constructor(address initialOwner) Ownable(initialOwner) {}

    /// @notice Register a new contributor with a percentage share
    /// @dev Only callable by owner, ensures total shares do not exceed 100
    function registerContributor(address _wallet, uint256 _share) external onlyOwner {
        require(_wallet != address(0), "check address");
        require(_share > 0 && _share <= 100, "Share must be between 1 and 100");
        require(contributors[_wallet].share == 0, "wallet already registered");
        require(totalShares + _share <= 100, "Error, Total shares exceed 100");

        contributors[_wallet] = Contributor(_share, 0);
        contributorList.push(_wallet);
        totalShares += _share;

        emit ContributorRegistered(_wallet, _share);
    }

    /// @notice Receive ETH into the contract
    receive() external payable {
        require(msg.value > 0, "No token sent");
        totalReceived += msg.value;
        emit PaymentReceived(msg.sender, msg.value);
    }

    /// @notice Allow contributors to withdraw their pending earnings
    function withdraw() external nonReentrant {
        Contributor storage c = contributors[msg.sender];
        require(c.share > 0, "Not a contributor");

        uint256 totalEarned = (totalReceived * c.share) / 100;
        uint256 paymentDue = totalEarned - c.released;
        require(paymentDue > 0, "Balance empty");

        c.released += paymentDue;
        totalReleased += paymentDue;

        (bool success, ) = msg.sender.call{value: paymentDue}("");
        require(success, "Transfer failed");

        emit PaymentWithdrawn(msg.sender, paymentDue);
    }

    /// @notice View how much a contributor can withdraw
    function pendingPayment(address _wallet) external view returns (uint256) {
        Contributor memory c = contributors[_wallet];
        if (c.share == 0) return 0;

        uint256 totalEarned = (totalReceived * c.share) / 100;
        return totalEarned - c.released;
    }

    /// @notice View all contributor addresses
    function getContributors() external view returns (address[] memory) {
        return contributorList;
    }
}
