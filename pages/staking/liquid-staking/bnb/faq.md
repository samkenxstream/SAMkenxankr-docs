### Which wallets are compatible with the ankrBNB (ex-aBNBc) tokens?

ankrBNB (ex-aBNBc) is an ERC-20 token that is compatible with Ethereum-based wallets like MetaMask.

### Are there any docs about the BNB staking and how I set up my wallet etc.?

Yes — there is a [user guide](https://www.ankr.com/docs/staking/liquid-staking/bnb/stake/).

### What is the minimum amount of BNB I can stake?

0.502 BNB: user asset — 0.5 BNB, plus the relayer fee for cross-chain transfer of your assets applied during the staking — 0.002 BNB. You must also count in the gas fee on top for sending the transaction.

### Why do I get less ankrBNB (ex-aBNBc) for my 1 BNB?

ankrBNB (ex-aBNBc) only changes in value, which is why the amount of ankrBNB you get when staking is calculated by the formula `stake * exchange_ratio`. The exchange ratio is calculated like this: `totals_supply_of_ankrbnb / (total_staked_bnb + total_rewards_for_staked_bnb - total_unstaked_ankrbnb)`.

### Is there a maximum amount I can stake?

You can stake at your discretion, unlimited.

### How long does it take to unstake my BNB?

You receive the unstaked BNB after the unbonding period of up to 7–10 days.

### How do I receive rewards?

aBNBb is not actively supported anymore. If you have any aBNBb in your wallet, you should receive ankrBNB automatically via airdrop.  

  

ankrBNB (ex-aBNBc) is a reward-bearing token, meaning its quantity stays the same from the moment of staking. Instead, it appreciates in value in relation to BNB, so the redemption price of 1 ankrBNB will grow over time because of reward accumulation.

### How soon after staking will I begin to receive rewards?

ankrBNB (ex-aBNBc) rewards are built into the token. Effectively, your rewards accumulate daily as ankrBNB grows in value to BNB.

### Does Ankr charge for the service?

5% are subtracted from your Liquid Staking rewards to cover the services and operations provided by Ankr. Your APY already includes the fee so no need to recalculate it, you get what you see.  

  

Note that relayer fee is a fee for cross-chain transfer of your assets applied during staking and is not an Ankr fee.

### If I click Unstake, does my stake immediately stop accumulating rewards?

Your stake immediately **stops** accumulating rewards once you clicked **Unstake**.

### Is there any risk from staking, like slashing or any penalties?

The only risk for stakers is missing out on rewards during any time a validator they staked with is “in jail” (slashed). Slashing is a protocol-level penalty associated with a validator failure if it validates an invalid transaction or goes offline. The delegated staked BNB is not slashed — slashing impacts only the self-stake of the validator. Ankr only delegates to trusted and reputable validator nodes to avoid any validator that would act maliciously.

### Is there any liquidity for my Liquid Staking tokens anywhere?

You can trade them in the listed liquidity pools on ANKR DeFi:

* [ankrBNB (ex-aBNBc)](https://www.ankr.com/staking/defi/?assets=ankrBNB)

You can also use your Liquid Staking tokens to:

* [Add liquidity on DEXs](https://www.ankr.com/docs/staking/defi/liquidity-pools/) and earn from commissions taken when users swap tokens, using the liquidity pool you're a part of.

* [Yield farm](https://www.ankr.com/docs/staking/defi/yield-farming/) and earn additional rewards in the form of liquidity pool tokens and further farm them.

* [Put your tokens in a vault](https://www.ankr.com/docs/staking/defi/vaults/) and automatically earn additional rewards in the form of one of both assets from the pair.

### Can I get staking metrics for my integration?

Yes, if you want to integrate Ankr Liquid Staking into your product, read [Liquid Staking Metrics](https://www.ankr.com/docs/staking/for-integrators/restful-api/staking-metrics/).
