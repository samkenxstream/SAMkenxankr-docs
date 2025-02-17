# AVAX staking mechanics

AVAX Liquid Staking lets the user stake their funds through the Ankr deposit address on the Avalanche network, accumulate rewards, and receive their stake+rewards when unstaking.

The following section explains staking requirements, fees, rewards, validators, smart contracts and function calls to interact with these smart contracts.


## Requirements

The requirements when staking are:

* Minimum value to stake — 1 AVAX.

* Stake can only be a whole number.

* Maximum value to stake — unlimited, at the user’s discretion.

The requirements when unstaking are:

* Minimum value to unstake — 0 AVAX.

* Unstaking allows fractional numbers.

* Maximum value to unstake — staked amount + % of the reward, up to the user’s balance.

* Release time — end of the current validating period, which is 4 weeks.


## Fees

The user pays 2% fee from the staking reward.

The user must also count in the gas price for outgoing transactions.


## Rewards

User's ankrAVAX grows steadily in value towards AVAX, but never changes in numbers. The formula can be described this way: `daily rewards / total share * user's share`.

Validators that work correctly and are online at least 80% of their validating period, receive rewards at the end of the validating period.


## Validators

On Avalanche, Ankr has three validators:
* [Validator #1](https://avascan.info/staking/validator/NodeID-NcZtrWEjPY7XDT5PHgZbwXLCW3LGBjxui)
* [Validator #2](https://avascan.info/staking/validator/NodeID-955GU1MqWL8yXAtoc8AsE7FNx4nGC9JyL)
* [Validator #3](https://avascan.info/staking/validator/NodeID-9CnrQBBFSkE2Xzfcz3Tk1e8iauq8iNR88)


## Smart contracts
Smart contracts involved in AVAX Liquid Staking are:
* [aAVAXb](https://snowtrace.io/address/0x6C6f910A79639dcC94b4feEF59Ff507c2E843929#code) — may be used to approve access to ankrAVAX as an intermediary functionality step.
* [ankrAVAX](https://snowtrace.io/address/0xc3344870d52688874b06d844e0c36cc39fc727f6#code) — keeps track of user shares and ankrAVAX supply share; ankrAVAX is an ERC-20-like token.
* [AvalanchePool](https://snowtrace.io/address/0x7BAa1E3bFe49db8361680785182B80BB420A836D#code) — implements staking and unstaking logic.


## Staking workflow
The workflow is both user and Ankr-driven. The user part is Step 1, the Ankr part is Steps 2–5. 

1. User sends AVAX to `AvalanchePool` on the Avalanche blockchain via the Ankr Staking Dashboard and MetaMask connected to the Avalanche blockchain. The function effectively called is `AlavalchePool::stakeAndClaimCerts()` for ankrAVAX. 

2. `AvalanchePool` emits an event saying the stake is received: `AlavalchePool::StakePendingV2(address indexed staker, uint256 amount, bool indexed isRebasing)`. `isRebasing` is false for `stakeAndClaimCerts` (ankrAVAX).

3. `AvalanchePool` calculates and stores/updates the number of aAVAXb or ankrAVAX the user can claim.

4. Ankr backend collects the staked AVAX transferring them from the smart contract to the deposit address controlled by Ankr. Then it makes a delegator or validator stake on the Avalanche blockchain.

5. ankrAVAX is minted automatically for the user's address.

## Ratio updates

Ankr backend updates the ratio daily and the user can see their updated aAVAXb balance. For ankrAVAX the updated ratio is used for recalculating value of ankrAVAX to AVAX.

## Unstaking workflow

The workflow is both user and Ankr-driven. The user part is Steps 1-2, the Ankr part is Steps 3–4.
1. The user inputs the number of ankrAVAX on the Ankr Staking Dashboard to unstake, and the frontend sends a request to the `AvalanchePool::claimCerts(uint256 amount)` smart contract to redeeem ankrAVAX for AVAX. `amount` specifies the amount AVAX to be released back to the user.
2. `AvalanchePool` sends a convert request to the `ankrAVAX` smart contract that burns the corresponding amount of ankrAVAX. `AvalanchePool` stores the convert request.
3. When the current validation period is over, Ankr backend checks if there any ankrAVAX-to-AVAX convert requests pending and requests `AvalanchePool` to serve them providing AVAX from unblocked stakes and received rewards, at the end of the current validating period.
4. `AvalanchePool` uses the AVAX received from the Ankr dedicated address to serve the pending convert requests. 