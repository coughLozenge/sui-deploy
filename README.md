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
Choose a mainnet RPC when propmted:
```
https://sui-mainnet.nodeinfra.com/

https://mainnet-rpc.sui.chainbase.online/

https://sui-mainnet-eu-1.cosmostation.io/

https://sui-mainnet-us-1.cosmostation.io/

https://rpc-mainnet.suiscan.xyz/mostation.io/
```
# Deploy simple NFT

Create a project folder:
```
sui move new nft-project
cd nft-project/src
nano nft.move
```
NFT contract:
```
module nft-project::my_nft {
    use sui::url::{Self, Url};
    use std::string;
    use sui::object::{Self, ID, UID};
    use sui::event;
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};

    /// An example NFT that can be minted by anybody
    struct MyNFT has key, store {
        id: UID,
        /// Name for the token
        name: string::String,
        /// Description of the token
        description: string::String,
        /// URL for the token
        url: Url,
        // TODO: allow custom attributes
    }

    // ===== Events =====

    struct NFTMinted has copy, drop {
        // The Object ID of the NFT
        object_id: ID,
        // The creator of the NFT
        creator: address,
        // The name of the NFT
        name: string::String,
    }

    // ===== Public view functions =====

    /// Get the NFT's `name`
    public fun name(nft: &MyNFT): &string::String {
        &nft.name
    }

    /// Get the NFT's `description`
    public fun description(nft: &MyNFT): &string::String {
        &nft.description
    }

    /// Get the NFT's `url`
    public fun url(nft: &MyNFT): &Url {
        &nft.url
    }

    // ===== Entrypoints =====

    /// Create a new my_nft
    public entry fun mint_to_sender(
        name: vector<u8>,
        description: vector<u8>,
        url: vector<u8>,
        ctx: &mut TxContext
    ) {
        let sender = tx_context::sender(ctx);
        let nft = MyNFT {
            id: object::new(ctx),
            name: string::utf8(name),
            description: string::utf8(description),
            url: url::new_unsafe_from_bytes(url)
        };

        event::emit(NFTMinted {
            object_id: object::id(&nft),
            creator: sender,
            name: nft.name,
        });

        transfer::public_transfer(nft, sender);
    }

    /// Transfer `nft` to `recipient`
    public entry fun transfer(
        nft: MyNFT, recipient: address, _: &mut TxContext
    ) {
        transfer::public_transfer(nft, recipient)
    }

    /// Update the `description` of `nft` to `new_description`
    public entry fun update_description(
        nft: &mut MyNFT,
        new_description: vector<u8>,
        _: &mut TxContext
    ) {
        nft.description = string::utf8(new_description)
    }

    /// Permanently delete `nft`
    public entry fun burn(nft: MyNFT, _: &mut TxContext) {
        let MyNFT { id, name: _, description: _, url: _ } = nft;
        object::delete(id)
    }
}
```
Send funds to the active address shown with:
```
sui client active-address
```
Deploy the contract:
```
sui client publish –gas-budget 1000000
```
Locate the package address and mint in an explorer:

![sui](https://github.com/coughLozenge/sui-deploy/assets/106449031/a019b8ce-ffcd-4069-88f8-168a8985d37b)


