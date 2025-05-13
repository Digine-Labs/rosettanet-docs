## First deposit

In order to use Rosettanet, your account has to be funded with STRKs and deployed on chain. So first you have to learn your Ethereum address actual Starknet contract address equivalent.
This can be done through the Rosettanet dashboard or manually retrieved via the Rosettanet registry contract.

## How to find the actual Starknet address?

To find your Starknet address, you need to interact with the Rosettanet smart contract. This process does **not** require you to pay any fees or connect your wallet.

Currently, the contract is deployed only on the **Sepolia** testnet. You can access it via the following block explorers:

- [Starkscan](https://sepolia.starkscan.co/contract/0x033d9ff643e9e964545dac77bb66d1f1828e11845fe7cac1960cb96e6b566833#read-write-contract)
- [Voyager](https://sepolia.voyager.online/contract/0x033d9ff643e9e964545dac77bb66d1f1828e11845fe7cac1960cb96e6b566833#readContract)

Under the **"Read Contract"** section, you will find a function named `precalculate_starknet_account`. To retrieve your Starknet address, simply enter your Ethereum address as the input to this function and click the **"Query"** button.

The returned value will be your corresponding Starknet address.

Alternatively, you can also use the `get_starknet_address` or `get_starknet_address_with_fallback` functions — don’t worry, all of them will return the same result. If the result of the `get_starknet_address` function is `0x0`, this means your account has not been deployed yet. In that case, you can use the fallback or precalculate version. Once your account is deployed, the `get_starknet_address` function will also return your Starknet address correctly.

## How to deploy my account?

Your account will be automatically deployed when you send your first transaction. Rosettanet intercepts this initial transaction and sends an account deployment transaction beforehand. Based on the response from the deployment transaction, it adjusts the nonce and other relevant fields of your original transaction accordingly, then submits it to the network.

As a user, you only need to send a regular transaction — no additional steps are required for account deployment. Rosettanet takes care of the entire deployment process behind the scenes.

## What happens next?

Once your first transaction is completed. You will be able to use your wallet with your favourite Starknet protocols.
