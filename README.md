# Quick guide to setup Sui on Ubuntu 20/22 and deploy contracts

# Rust
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
# Prerequisites
```
sudo apt-get install curl git-all cmake gcc libssl-dev pkg-config libclang-dev libpq-dev build-essential
```
# Sui
```
cargo install --locked --git https://github.com/MystenLabs/sui.git --branch mainnet sui
```
# Connect
```
sui client
```
Choose a mainnet RPC when prompted:
```
https://sui-mainnet.nodeinfra.com/

https://mainnet-rpc.sui.chainbase.online/

https://sui-mainnet-eu-1.cosmostation.io/

https://sui-mainnet-us-1.cosmostation.io/

https://rpc-mainnet.suiscan.xyz
```
# Deploy simple NFT

Create a project folder:
```
sui move new nft-project
cd nft-project/sources
nano nft.move
```
NFT contract:

https://github.com/coughLozenge/sui-deploy/blob/main/src/nft.move


Send funds to the active address shown with:
```
sui client active-address
```
Deploy the contract:
```
sui client publish –gas-budget 1000000
```
In the explorer, locate the package address and mint:

![sui](https://github.com/coughLozenge/sui-deploy/assets/106449031/a019b8ce-ffcd-4069-88f8-168a8985d37b)


