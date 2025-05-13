# Rosettanet Accounts

Rosettanet has its own account smart contracts. These account contracts designed to validate and execute transactions signed as Legacy or EIP1559 transaction types. Every Ethereum account is matched with actual starknet account addresses, however Rosettanet will handle the transaction so users won't notice the difference.

## What is difference between Ethereum address and Starknet contract address?

While Ethereum and Starknet both use addresses to identify accounts and contracts, they operate under different account models and deployment requirements.

### 1. **Type of Account**

- **Ethereum:**  
  Ethereum has two types of accounts:

  - **Externally Owned Accounts (EOAs):** Controlled by private keys. These are standard user wallets like MetaMask.
  - **Contract Accounts:** Deployed with bytecode and controlled by code.

- **Starknet:**  
  On Starknet, **all accounts are smart contracts**. There are no EOAs. This means even user wallets are implemented as contract instances with programmable logic. This model allows for advanced features like multisig, session keys, and account abstraction directly at the protocol level. Every interaction with Starknet, including signing and transaction validation, is routed through these account contracts.

### 2. **Deployment**

- **Ethereum:**  
  EOAs exist by default once a user generates a public/private key pair. No on-chain deployment is required.

- **Starknet:**  
  Starknet accounts must be explicitly deployed to the network before they can be used. An address on Starknet does not become active until its associated contract is deployed. This allows the account to include custom verification logic or wallet behavior as part of the contract's code.

## How should I transfer my first funds into Rosettanet account?

Before transferring any funds, you need to know your Rosettanet Starknet address. We've explained how to find it in [this section](/users/first.html#how-to-find-the-actual-starknet-address).

Once you have your Starknet address, you can safely transfer funds to it from another Starknet wallet or faucet. **Please avoid sending funds directly from centralized exchanges (CEXs)**.

If you have another wallet that already holds a Rosettanet account, you can also transfer funds using your **Ethereum address** directly. For example, open MetaMask, click the “Send” button, and paste your Ethereum wallet address into the "To" field.

> Note: This method only works if both wallets are connected to the Rosettanet network and the accounts have already been deployed.

## What happens if I mistakenly send funds to my Rosettanet account from any CEX or another Starknet account?

## How can I learn my actual Starknet account address?
