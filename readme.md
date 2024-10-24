# CW3 multisig contracts

## Summary

Many projects use multiple signatures--aka multisig--to control their treasuries and secure their contracts.What is CW3? CW3 is the third CosmWasm standard, documented in the cw-plus repo. It defines a standard for a contract. CW3 is a smart contract implementation of multiple signatures. Each CW3 has a configuration of who can sign and how many signatures are needed for a proposal to pass. In practice, all wallets we've set up have been one of these simple approaches. 2 of 3 is considered the minimal multisig, and 3 of 5 is probably the most common.

**Fixed vs flex**

The cw-plus repo contains two basic approaches to setting up a CW3:
**Fixed**: when you create the contract, you give it a hard-coded list of members and their voting power. After that, they can never be changed.**Flex**: first you create a CW4 contract, which maintains a group of users, and then create a CW3-flex that refers to that CW4.
 

**Proposal process:** One of the members creates a new proposal. You can do anything from sending coins to executing smart contract messages.
Other members can vote yes, no, or abstain on the proposal.
If enough people vote yes, the proposal is passed. At that point, anyone (even non-members) can execute the proposal.

**For more details check these links:**

https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw3-fixed-multisig
https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw3-flex-multisig
https://github.com/CosmWasm/cw-plus/tree/main/contracts/cw4-group

---
### Task:

1.- Upload the CW3-fixed contract to Osmosis testnet

2.- Instantiate a new 2-of-3 multisig contract using three Osmosis addresses you create yourself

3.- Send 5,000 uosmo into the multisig contract

4.- Create a multisig proposal to send 2,500 uosmo from the contract to one of your wallet addresses

5.- Vote on and execute the proposal
	
> **Note**: Use the cosmos CLI tool from cosmos-rs 

### Preparing enviroment:

*   #### Download last stable version of cw3-fixed multisig from official repo https://github.com/CosmWasm/cw-plus
*   #### Install cosmos-rs: 
```bash
cargo install --git https://github.com/fpco/cosmos-rs cosmos-bin --locked
```
*   #### Get at least four wallets from Osmosis Testnet

### Voters
```bash
Voter 1 {
    mnemonic = "light story clarify essay twin ritual range train comic body wolf path"
    wallet_address = osmo18c8rrfn3wny89kpz9qxmrvlucjpehtvpq3r0q8
    balance = 1000000uosmo
}

Voter 2 {
    mnemonic = "mixed light palm tumble certain remain maple year put destroy slush snap"
    wallet_address = osmo1ytg6p42m2yv6nxts035y7zr4l7ezlcafs646n0
    balance = 1000000uosmo
}

Voter 3{
    mnemonic = "glove elder woman dynamic false wealth dove leopard fury upon industry buyer"
    wallet_address = osmo1jzctzlf3cjprece9wgwgh7p26vr78w7h32wwwe
    balance = 1000000uosmo
}

```

### Destination Wallet
```bash
mnemonic = "daring reunion program lunar simple arena check practice level notice lamp asthma"
wallet_address = osmo1kuu03ag09hc57p9m5wgp9rn8s3fx038yuvcxy3
balance = 0uosmo
```
---
## Let's start
**Define env. variables:** setup the connection to Osmosis TestNet
```bash
export COSMOS_GRPC="https://grpc.testnet.osmosis.zone" COSMOS_CHAIN_ID="osmo-test-5" COSMOS_GAS_COIN="uosmo" COSMOS_HRP="osmo"
```
**Check balance of destination wallet**
```bash
cosmos bank print-balances osmo1kuu03ag09hc57p9m5wgp9rn8s3fx038yuvcxy3
```
**Upload contract:** Use the *.wasm contract file that you downloaded. You will get a **Code ID**, which is the contract code id 
```bash
cosmos contract store-code --wallet "light story clarify essay twin ritual range train comic body wolf path" cw3_fixed_multisig_official.wasm 

Code ID: 11299
```
**Step 4: Instantiate contract in order to work with it:** In order to work with the contract you need to create a instance of the contract, you will get the **contract address**. Here you define 3 voters and only 2 are required to proposal by approved. 
```bash
cosmos contract instantiate --wallet 'light story clarify essay twin ritual range train comic body wolf path' 11299 'cw3-fixed' '{"voters":[{"addr":"osmo18c8rrfn3wny89kpz9qxmrvlucjpehtvpq3r0q8","weight":1}, {"addr":"osmo1ytg6p42m2yv6nxts035y7zr4l7ezlcafs646n0","weight":1}, {"addr":"osmo1jzctzlf3cjprece9wgwgh7p26vr78w7h32wwwe","weight":1}], "threshold":{"absolute_count":{"weight":2}}, "max_voting_period":{"time":604800}}'

Contract: osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2
```

**Verify contract:** In order to get more info about creator and admin as well as code id and label.
```bash
cosmos contract info osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2

code_id: 11299
creator: osmo18c8rrfn3wny89kpz9qxmrvlucjpehtvpq3r0q8
admin: osmo18c8rrfn3wny89kpz9qxmrvlucjpehtvpq3r0q8
label: cw3-fixed
```
**Send money to contract from your wallet.** All good at this point
```bash
cosmos bank send --wallet "light story clarify essay twin ritual range train comic body wolf path" osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2 "5000uosmo"

5D00E4ADA6A76D6FF772C41B3BDA2AD1060C5E42FE4454A16D3D75B99E137B78
```

**Send money from contract to one of the wallets.** Here we must create a proposal in order to send money from the contract to the destination wallet. This proposal must be approved by 2 voters 

**Execute Proposal.** See all fields and where you are sending the money. We will get a **Transaction hash** is the proposal was set
```bash
cosmos contract execute --wallet "light story clarify essay twin ritual range train comic body wolf path" osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2 '{"propose":{"title":"Send 2,500 uosmo","description":"Sending 2500uosmos to wallet", "msgs":[{"bank":{"send":{"to_address":"osmo1kuu03ag09hc57p9m5wgp9rn8s3fx038yuvcxy3", "amount":[{"denom": "uosmo","amount":"2500"}]}}}], "latest":{"never":{}}}}'

Transaction hash: 3E66D86D78A181E3C2C9625D8F77582176C18443C190D2B18437E0548E87CA64
Raw log: 
```
**Now some voters must vote in oder to execute or not the proposal.** Let's query the contract in order to get the proposal_id. You will get a list of proposals
```bash
cosmos contract query osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2 '{"list_proposals":{"start_after": null,"limit":10}}'
we will get this response:

{"proposals":[{"id":1,"title":"Send 2,500 uosmo","description":"Sending 2500uosmos to wallet","msgs":[{"bank":{"send":{"to_address":"osmo1kuu03ag09hc57p9m5wgp9rn8s3fx038yuvcxy3","amount":[{"denom":"uosmo","amount":"2500"}]}}}],"status":"open","expires":{"at_time":"1730331115082817627"},"threshold":{"absolute_count":{"weight":2,"total_weight":3}}}]}
```
As we can see we got the proposal id: **1**. Now voters can execute their vote

**Voter 2 say yes.** If execution success you'll get a transaction hash 
```bash
cosmos contract execute --wallet "mixed light palm tumble certain remain maple year put destroy slush snap" osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2 '{"vote":{"proposal_id": 1, "vote": "yes"}}'

Transaction hash: D6A13053242E19A4E51A6D85DA3085358A3AA85AD1BFCC774187D954C5A5DDE0
Raw log: 
```

**AT THIS TIME WE ACCEPTED THE PROPOSAL BECAUSE VOTER 1 CREATED THE TRANSACTION THAT'S 1 VOTE AND VOTER 2 SAY 'YES'. OUR PROPOSAL HAVE BEEN APPROVED**

**You can start over, created a new proposal and make Voter 2 vote 'no' and Voter 3 vote 'yes'**
```bash
cosmos contract execute --wallet "glove elder woman dynamic false wealth dove leopard fury upon industry buyer" osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2 '{"vote":{"proposal_id": 1, "vote": "yes"}}'

Transaction hash: D14E3A4BB919C68854E622566D0BCC3E25F5D9A7C8B55B8A4E1E7E0841994896
Raw log: 
```

**Execute proposal.** In order to make the transaction.
```bash
cosmos contract execute --wallet "light story clarify essay twin ritual range train comic body wolf path" osmo1fjxc6ytrnek59rw3shllgjkdudwqtcwmwlehjyg7cuc5q3zxrmtsxadgt2 '{"execute": {"proposal_id": 1}}'

Transaction hash: 782DBF36413478271A6D7EDA14112D1CE9A3E9CA8BB7D5749B445633615D5E0F
Raw log: 
```

**Check balance of destination addr**
```bash
cosmos bank print-balances osmo1kuu03ag09hc57p9m5wgp9rn8s3fx038yuvcxy3
```

### And that it's :)