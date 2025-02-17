# FTM Liquid Staking API

To integrate with FTM Liquid Staking, use the smart contract functions and examples below.

### Stake FTM and claim ankrFTM

#### `stakeAndClaimCerts()`
 
Stakes the `msg.value` of FTM and claims ankrFTM for it.

##### Smart contract

* [Mainnet FantomPool Proxy](https://ftmscan.com/address/0x84db6eE82b7Cf3b47E8F19270abdE5718B936670)
* [Testnet FantomPool Proxy](https://testnet.ftmscan.com/address/0x7B72E8117E69951F1b00178016EEaEE4ce715f28)

##### Example

* [Mainnet FantomPool Proxy — 0x84db6eE82b7Cf3b47E8F19270abdE5718B936670](https://ftmscan.com/address/0x84db6eE82b7Cf3b47E8F19270abdE5718B936670)
* [Testnet FantomPool Proxy — 0x7B72E8117E69951F1b00178016EEaEE4ce715f28](https://testnet.ftmscan.com/address/0x7B72E8117E69951F1b00178016EEaEE4ce715f28)

### Unstake ankrFTM and claim FTM

#### `burnCerts(amount)`
 
Burns ankrFTM and gets FTM for the burned ankrFTM.

##### Parameters 

`amount` (uint256, required) — amount of ankrFTM to be unstaked.

##### Smart contract

* [Mainnet FantomPool Proxy — 0x84db6eE82b7Cf3b47E8F19270abdE5718B936670](https://ftmscan.com/address/0x84db6eE82b7Cf3b47E8F19270abdE5718B936670)
* [Testnet FantomPool Proxy — 0x7B72E8117E69951F1b00178016EEaEE4ce715f28](https://testnet.ftmscan.com/address/0x7B72E8117E69951F1b00178016EEaEE4ce715f28)

##### Example

* [Mainnet live transaction example](https://ftmscan.com/tx/0x303e68588bf68dbfd515a7d1b46198c18b8b978b1bee540ff8386e871c7dc4d9)
* [Testnet live transaction example](https://testnet.ftmscan.com/tx/0x7ca2d6bda3db3d4c60119d6a1bc5e9245d24066669a30caafa275d147cf3c9fc)


### Get APR

#### `averagePercentageRate(uint256 day, address addr)`

Gets the APR for ankrFTM.

The formula is best expressed by an example.

With `3` provided as the depth, the APR = `((((day 3 - day 2) / day 3) * 100) + ((day 2 - day 1) / day 2) * 100) + ((day 1 - current day) / day 1) * 100) / 3) * 365`.

##### Parameters

* `day` (uint256, required) — number of days to get the APR for. Max value — 7 days.
* `address` (address, required) — address of the token contract. The address of ankrFTM is `0xCfC785741Dc0e98ad4c9F6394Bb9d43Cd1eF5179`.

##### Smart contract

* [Mainnet RatioFeed contract for ankrFTM](https://ftmscan.com/address/0xb42bf10ab9df82f9a47b86dd76eee4ba848d0fa2)

##### Example

`averagePercentageRate` being a read function, we suggest you visit the links and make a query entering a desired number of days to see an example.
* [Mainnet RatioFeed contract for ankrFTM function](https://ftmscan.com/address/0xb42bf10ab9df82f9a47b86dd76eee4ba848d0fa2#readProxyContract#F2)

### Get staking metrics

To integrate Ankr Staking metrics into your dashboards or use metrics like APY in your products, read [Liquid Staking Metrics](/staking/for-integrators/restful-api/staking-metrics/).


