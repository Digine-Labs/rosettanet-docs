# Introduction


## What is Rosettanet?

Rosettanet is a middleware architecture that reads and executes Starknet transactions like Ethereum transactions. It makes it possible with having its own node and its own account contracts. Anyone can deploy the Rosettanet node without any trusted setup. Any Ethereum tooling or wallet can keep working since it is connected to the Rosettanet node with its own custom chain ID.

## What Rosettanet brings to Starknet?

- Any Starknet protocols can start supporting Ethereum wallets such as Metamask, etc.
- Existing Ethereum toolings like Web3js and ethers can be used on Starknet.
- Allows sending & executing transactions with Ethereum signatures.
- Seamless migration for users from Ethereum or any other EVM chain.

## Project structure and related repositories

- [Rosettanet](https://github.com/Digine-Labs/rosettanet): Rosettanet node written in Typescript
- [Rosettanet Accounts](https://github.com/Digine-Labs/rosettacontracts): Starknet account smart contracts that work with Ethereum-signed transactions.
- [Rosettanetjs](https://github.com/Digine-Labs/rosettanetjs): Javascript library that includes functions to integrate Rosettanet to frontend.

## Community Channels

- [Twitter](https://x.com/rosettanetstark)
- [Telegram](https://t.me/rosettanet)