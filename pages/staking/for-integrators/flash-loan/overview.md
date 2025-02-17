import { Callout } from "components";

# Flash loans

## What are flash loans?
Flash Loans are uncollateralized loans that allow the user to borrow HAY as long as the borrowed amount (and a fee) is returned before the end of the transaction.

<Callout type="warning">
To use flash loans and get profit from them, you need a good understanding of BNB Chain (and Smart Chain), programming, and smart contracts.
</Callout>

## Application of flash loans
There are various applications of flash loans.
An obvious example is arbitrage between assets, where the user gets a flash loan in BNB from the Ankr's BNB swap pool, immediately swaps the BNB for another asset on a DEX, then immediately swaps the obtained asset for BNB on another DEX where the asset's ratio is higher, and repays Ankr the flash loan + interest, keeping the difference.
 
## Involved entities
* SwapPool — Ankr's smart contract implementing a pool with the flash loan functionality.
* flashBorrower (flashBorrower.sol) — the smart contract, which EOA uses to interact with SwapPool through. A stub example of the smart contract is available in the code example below.
* EOA (Externally Owned Account) — an account that interacts with the copy of flashBorrower they deploy. Effectively, EOA is a developer who copies flashBorrower, modifies, and deploys it on BSC to interact with SwapPool through.

SwapPool can be used to borrow and repay BNB, with a fee, in one transaction. EOA needs to interact with their own deployed copy of flashBorrower, which in turn interacts with SwapPool.

## Flash loan fee

The fee is 0.5% with the minimum of 10000 wei. 

## Step-by-step

### 1. Set up and deploy flashBorrower
Your contract must conform to the ERC3156FlashBorrower interface by implementing the `onFlashLoan()` function. Find the interface code right after the stub example. 

To interact with flashLender, your contract must implement `flashBorrow(token, amount)` and `onFlashLoan(initiator, token, amount, fee, data)`, which is a callback function called during the execution of `flashLoan()`. 
Implement any custom logic within `onFlashLoan()`.
Here's an example stub contract you can look through.
Use it as a reference, create your own flashBorrower inheriting the interfaces from the example, and populate it with your own custom logic.
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "../interfaces/IERC3156FlashBorrower.sol";
import "../interfaces/IERC3156FlashLender.sol";

contract FlashBorrower is IERC3156FlashBorrower {
    enum Action {NORMAL, OTHER}

    IERC3156FlashLender lender;

    constructor (IERC3156FlashLender lender_) {
        lender = lender_;
    }

    /// @dev ERC-3156 Flash loan callback
    function onFlashLoan(
        address initiator,
        address token,
        uint256 amount,
        uint256 fee,
        bytes calldata data
    ) external override returns(bytes32) {
        require(token != address(0));
        require(amount != 0);
        require(fee != 0);
        require(
            msg.sender == address(lender),
            "FlashBorrower: Untrusted lender"
        );
        require(
            initiator == address(this),
            "FlashBorrower: Untrusted loan initiator"
        );
        (Action action) = abi.decode(data, (Action));
        if (action == Action.NORMAL) {
            // do one thing
        } else if (action == Action.OTHER) {
            // do another
        }
        return keccak256("ERC3156FlashBorrower.onFlashLoan");
    }

    /// @dev Initiate a flash loan
    function flashBorrow(
        address token,
        uint256 amount
    ) public {
        bytes memory data = abi.encode(Action.NORMAL);
        uint256 _allowance = IERC20(token).allowance(address(this), address(lender));
        uint256 _fee = lender.flashFee(token, amount);
        uint256 _repayment = amount + _fee;
        IERC20(token).approve(address(lender), _allowance + _repayment);
        lender.flashLoan(this, token, amount, data);
    }
}
```

ERC3156FlashBorrower interface code example:
``` 
// SPDX-License-Identifier: AGPL-3.0-or-later
// Copyright (C) 2021 Dai Foundation
//
// This program is free software: you can redistribute it and/or modify
// it under the terms of the GNU Affero General Public License as published by
// the Free Software Foundation, either version 3 of the License, or
// (at your option) any later version.
//
// This program is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU Affero General Public License for more details.
//
// You should have received a copy of the GNU Affero General Public License
// along with this program.  If not, see <https://www.gnu.org/licenses/>.

pragma solidity ^0.8.10;

interface IERC3156FlashBorrower {

    /**
     * @dev Receive a flash loan.
     * @param initiator The initiator of the loan.
     * @param token The loan currency.
     * @param amount The amount of tokens lent.
     * @param fee The additional amount of tokens to repay.
     * @param data Arbitrary data structure, intended to contain user-defined parameters.
     * @return The keccak256 hash of "ERC3156FlashBorrower.onFlashLoan"
     */
    function onFlashLoan(
        address initiator,
        address token,
        uint256 amount,
        uint256 fee,
        bytes calldata data
    ) external returns (bytes32);
}
```

#### Functions
* `flashBorrow(address token, uint256 amount)` — function that start the flash loans flow.
* `onFlashLoan(address initiator, address token, uint256 amount, uint256 fee, bytes calldata data)` — the callback function called by `flashLoan()`. Use it to implement anything you want to do with the borrowed BNB tokens. 

<Callout type="info">
onFlashLoan() must return the CALLBACK_SUCCESS hash as shown in the following extract from the flashBorrower stub.
```
require(receiver.onFlashLoan(msg.sender, token, amount, fee, data) == CALLBACK_SUCCESS, "Flash/callback-failed");
```
</Callout>

#### Parameters/Constants
1. `initiator` (address) — address of the flashBorrower smart contract deployed by EOA.
2. `token` (address) — address of the BNB ERC-20 token that EOA flash-loans.
3. `amount` (uint256) — amount of the flash loan.
4. `fee` (uint256) — the fee paid for the flash loan, by flashBorrower. 
5. `data` (bytes calldata) — rudimentary non-used parameter left not to change the `onflashLoan()` signature.

### 2. Understand how to interact with SwapPool

#### Functions
1. `maxFlashLoan(address token)` — returns “max” if token is a supported asset. Used in checking if the desired loan amount is lower than the max permitted amount.  
2. `flashFee(address token, uint256 amount)` — applies “toll” on `amount` and returns if token is a supported asset.
3. `flashLoan(IERC3156FlashBorrower receiver, address token, uint256 amount, bytes calldata data)` — mints token `amount` to `receiver` with extra data (if any), and expects a return equal to `CALLBACK_SUCCESS`.

#### Parameters/Constants
* `CALLBACK_SUCCESS` — Hash of custom string, returned on success.
* `token` (address) — address of the BNB ERC-20 token that EOA flash-loans.
* `amount` (uint256) — amount of the flash loan.
* `receiver` (IERC3156FlashBorrower) — address of the flashBorrowed deployed by the EOA.
* `data` (bytes calldata) — rudimentary non-used parameter left not to change the `flashLoan()` signature.  

### 3. Interact with SwapPool
#### Addresses

You can find SwapPool by the following addresses:
* [Testnet SwapPool — 0x0988fBea4B84C135663d0504A5EF9f5447B0F2ED](https://testnet.bscscan.com/address/0x0988fBea4B84C135663d0504A5EF9f5447B0F2ED)
* [Mainnet SwapPool — 0xaf565322d1c3d681F1e8884B4781f62368fc2344](https://bscscan.com/address/0xaf565322d1c3d681F1e8884B4781f62368fc2344)

A typical interaction follows this workflow:
1. An EOA calls flashBorrow(token, amount) on a borrowing contract flashBorrower.sol.
2. flashBorrower approves, in advance, SwapPool to access an amount of BNB equal to the loan + fee to repay the loan in the future. 
3. flashBorrower calls the flashLoan(receiver, token, amount, data) function on SwapPool which mints a specified amount to the flashBorrower.
   1. The same function flashLoan(receiver, token, amount, data) then calls back the onFlashLoan(initiator, token, amount, fee, data) function on the flashBorrower, which implements the custom logic of whatever the EOA wants to do with the borrowed BNB. 
   2. After running all the custom logic inside, onFlashLoan() returns the KECCAK256 of “ERC3156FlashBorrower.onFlashLoan" if its execution was successful. **The custom logic inside `onFlashLoan()` is thought-up and implemented completely by the EOA.**
4. Finally, SwapPool transfers the loan amount + fee from the flashBorrower to itself to repay the loan. The access to this amount was preauthorized by the flashBorrower at #2. 

## Usage example
A close-to-life usage example is coming soon.

