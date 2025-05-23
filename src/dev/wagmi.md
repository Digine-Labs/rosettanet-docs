# Wagmi

Integrating `wagmi` with Rosettanet is straightforward and works almost exactly as it does on Ethereum with just a few minor adjustments.

You can continue to use familiar hooks and patterns from `wagmi`, and in some cases, you may also need to use utility functions from the `viem` library to handle lower-level tasks or data formatting as needed.

## Connecting to Rosettanet Chain;

First, you need to define a custom `Rosettanet Chain` and then create a corresponding Wagmi configuration.
You can use the code snippet below as-is, just replace the placeholder with a valid `Rosettanet Sepolia RPC URL`.

The custom Rosettanet chain will be added to the list of chains recognized by `viem`.

```js
import { defineChain } from 'viem'
import { http, createConfig } from '@wagmi/core'
import { sepolia } from '@wagmi/core/chains'
import { injected } from 'wagmi/connectors'

const rosettanetSepolia = defineChain({
  id: 1381192787,
  caipNetworkId: 'eip155:1381192787',
  chainNamespace: 'eip155',
  name: 'Rosettanet',
  nativeCurrency: {
    decimals: 18,
    name: 'Starknet Token',
    symbol: 'STRK',
  },
  rpcUrls: {
    default: {
      http: ['Rosettanet Sepolia RPC URL'],
    },
  },
  blockExplorers: {
    default: { name: 'Voyager Sepolia', url: 'https://sepolia.voyager.online' },
  },
  contracts: {
  },
})

export const config = createConfig({
  chains: [rosettanetSepolia, sepolia],
  connectors:[injected()]
  transports: {
    [rosettanetSepolia.id]: http(),
    [sepolia.id]:http(),
  },
});
```

Now that the configuration is complete, the next step is to wrap your application with the required providers:

* `Wagmi Provider` from `wagmi`

* `QueryClientProvider` from `Tanstack Query`

This ensures that both the wagmi hooks and query caching mechanisms function properly throughout your app.

```js
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { WagmiProvider } from 'wagmi'

const queryClient = new QueryClient()

function App() {
  return (
    <WagmiProvider config={config}>
      <QueryClientProvider client={queryClient}>
        {/** ... */}
      </QueryClientProvider>
    </WagmiProvider>
  )
}
```

Once the providers are set up, your DApp is fully connected and ready to use `wagmi` throughout the application.

To connect browser-injected wallets (such as MetaMask), you can use the `useConnect` hook provided by `wagmi`:

## Interacting with a Smart Contract

Using `wagmi` with Rosettanet is seamless and mirrors the typical Ethereum development experience, with only minimal adjustments.

Below is a simple example of how to read data from a smart contract using the `useReadContract()` hook, exactly as you would in a standard Ethereum-based DApp:

```js
const TestAccount1 = "account-address";
const TestContract = "contract-address";

// A minimal ERC-20 ABI for demonstration purposes
const ERC20_ABI = [
    'function balanceOf(address owner) view returns (uint256)',
    'function decimals() view returns (uint8)'
];

// Checking decimals of ERC20 contract

const result = useReadContract({
    ERC20_ABI,
    address: TestContract,
    functionName: 'decimals',
})

---
// Checking balance of TestAccount1

const result = useReadContract({
    ERC20_ABI,
    address: TestContract,
    functionName: 'balanceOf',
    account: TestAccount1,
    args: [TestAccount1]
})
```

## Sending Transactions

To send a transaction with `wagmi`, you can use the `useSendTransaction` hook. Below is an example demonstrating how to use it. 

```js
import { useSendTransaction, useAccount } from 'wagmi'
import { parseEther } from 'viem'
import { config } from "path-to-config"

const { sendTransaction } = useSendTransaction()
const { address } = useAccount()

const toAddress = "to-address"

const transaction = await sendTransaction(config, {
    to: toAddress
    value: parseEther(1) // 1 STRK
    chainId: 1381192787,
    account: address,
})
```

### Transactions with `data`

If your transaction includes a `data` payload, you first need to register your smart contract with the Rosettanet registry. You can do this by calling the `register_contract` function on the [Rosettanet Sepolia smart contract](https://sepolia.voyager.online/contract/0x065a6238502254a31072c53bedf5046cbb626ce49cd49ba20e206b35d5aed0ad#writeContract).

After registration, you can retrieve the corresponding Ethereum-compatible address of your Starknet contract using the `get_ethereum_address` function.

When constructing your transaction:

* The `to` field should be set to the Ethereum address returned from the registry.

* The `data` field must be manually encoded.

This encoding can be done using `encodeFunctionData()`, a function available in the `viem` library. (using a minimal ERC-20 ABI for demonstration):

```js
import { useSendTransaction, useAccount } from 'wagmi'
import { parseEther , encodeFunctionData } from 'viem'
import { config } from "path-to-config"

const { sendTransaction } = useSendTransaction()
const { address } = useAccount()

const ERC20_ABI = [
    'function balanceOf(address owner) view returns (uint256)',
    'function decimals() view returns (uint8)',
    'function transfer(address to, uint256 amount) public returns (bool)'
];

const calldata = encodeFunctionData({
    abi: ERC20_ABI,
    functionName: "transfer",
    args: ["transfer-to-address", parseEther(1)]
})

const transaction = await sendTransaction(config, {
    to: "ethereum address from get_ethereum_address()"
    chainId: 1381192787,
    account: address,
    data: calldata
})
```

### Using `multicall`

If you want to use `multicall` feature from starknet, you need to pass `to` field as `0x0000000000000000000000004645415455524553`. Below is an example of how to transfer ERC-20 tokens to two different accounts using a `multicall`:

```js
import { useSendTransaction, useAccount } from 'wagmi'
import { parseEther , encodeFunctionData } from 'viem'
import { config } from "path-to-config"

const { sendTransaction } = useSendTransaction()
const { address } = useAccount()

const MULTICALL_ABI = [
    'function multicall((uint256,uint256,uint256[])[])'
];

const receiver1SnAddress = await precalculateStarknetAddress('reciever-1-eth-address');
const receiver2SnAddress = await precalculateStarknetAddress('reciever-2-eth-address');

const calldata = encodeFunctionData({
    abi: MULTICALL_ABI,
    functionName: "multicall",
    args: [[ 
        [
            ERC20_TOKEN_ADDRESS,
            '0x0083afd3f4caedc6eebf44246fe54e38c95e3179a5ec9ea81740eca5b482d12e', // transfer function entrypoint
            [receiver1SnAddress, 
            '1000', // amount.low
            '0' // amount.high
            ]
        ],
        [
            ERC20_TOKEN_ADDRESS,
            '0x0083afd3f4caedc6eebf44246fe54e38c95e3179a5ec9ea81740eca5b482d12e', // transfer function entrypoint
            [receiver2SnAddress, 
            '2000', // amount.low
            '0'// amount.high
            ]
        ]
    ]]
})

const transaction = await sendTransaction(config, {
    to: "0x0000000000000000000000004645415455524553"
    chainId: 1381192787,
    account: address,
    data: calldata
})
```
