# Introduction

Icedancer is building the first webassembly powered general purpose Solana Indexer.

Write your subgraph in Rust and deploy it on our scalable cloud. Forget about server management, RPCs, geyser plugins, databases and API gateteways. Icedancer manages the muck for you.

Icedancer is a **true indexer** where you can deploy custom indexing logic, it's not an API to anchor IDLs for account data, nor a cookie cutter API for NFTs.

## Demo

## Why indexing?

[Banana Swap](https://github.com/icedancer-io/banana-swap) emits `SwapEvent` whenever a swap happens.

```rs
use anchor_lang::prelude::*;

declare_id!("3Zg1z6Yfuv4vAkVcm1j3jxhb1QqF3DPzn7uaf3jeNqwo");

#[program]
pub mod banana_swap {
    use super::*;

    pub fn swap(ctx: Context<Swap>, amount_in: u64, amount_out: u64) -> Result<()> {
        emit!(SwapEvent {
            market: ctx.accounts.market.key(),
            amount_in,
            amount_out
        });

        Ok(())
    }
}

#[derive(Accounts)]
pub struct Swap<'info> {
    /// CHECK: A market account
    pub market: UncheckedAccount<'info>
}

#[event]
struct SwapEvent {
    market: Pubkey,
    amount_in: u64,
    amount_out: u64
}
```

We need analytics on the exchange's performance. Data can be obtained from two sources

1. **Accounts**: Token account balances can tell us how much liquidity is present in the DEX.
2. **Events**: `SwapEvent` tells us the market address, input amount and output amount of each swap. This is data at a per-transaction level.

What if we need more complex stats like

1. Lifetime volume of the exchange
2. Best performing liquidity pools
3. Tokens with least volume

It becomes infeasible to calculate and store these stats in accounts due to storage and cost constraints. The right way is to emit events, listen to them off-chain and calculate these metrics. This is called indexing.

The icedancer demo calculates the lifetime output token volume of banana swap.

## Why yet another Solana indexing protocol (YASIP)?

Projects like Metaplex, shyft.to, Helius approached indexing with a cookie cutter approach. They provide custom indexing APIs for

1. NFTs
2. SPL tokens
2. Popular DeFi protocols

Need something custom? Building the next Uniswap? You are in bad luck. Running an archival node, geyser plugin, indexing server, database and API gateway is not trivial. Development and operations effort is high, data processing and storage is not cheap given Solana's high TPS and activity.

Icedancer lets you deploy WebAssembly indexer modules on its servers, abstracting away the RPC and database infrastructure. The cost of indexing is reduced because resources are pooled across different subgraphs.
