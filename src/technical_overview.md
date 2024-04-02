# Technical overview

## Architecture

### Indexing without Icedancer

![](./assets/without-icedancer.drawio.svg)

- Every project requiring indexing runs it's own archival solana node, a custom Geyser plugin, a database and an API gateway.

- This results in duplication of effort and high cost.

- Given the high speed and size of Solana transactions, the hardware requirement is high.

### Indexing with Icedancer

![](./assets/architecture.drawio.svg)

- RPC, geyser plugin, database and API gateway is pooled. This results in significant cost savings.

- Each project deploys its subgraph to Icedancer, which is executed using Wasmer. Transactions having the subgraph's linked account are passed to the WASM module.

- The WASM module processes the input. It has the ability entities to database.

- Database entities can be consumed via a GraphQL API.

## Tech stack

- Forked and optimized Postgres geyser plugin

- Define entities and API in GraphQL

- Subgraphs written in Rust and compiled to WASM

- Indexing server written in Rust that executes subgraphs with Wasmer

- Postgres database for storage
