# Integration of Rosettanet into Starknet DApps

Rosettanet integration is getting added to `starknet.js` (6.24.1) and `get-starknet` (v4). With these integrations Rosettanet will work as `WalletAccount` in `starknet.js`.

## Connecting to Wallet

DApps can connect to wallets using the `connect()` function from `get-starknet`:

```jsx
import { connect } from "@starknet-io/get-starknet";

const [selectedAccount, setSelectedAccount] = useState(null);

function handleConnect() {
  return async () => {
    const res = await connect();
    setSelectedAccount(res);
  };
}

return <button onClick={handleConnect()}>Connect With get-starknet</button>;
```

This is a standard implementation of `get-starknet` in DApps. To use the wallet with `starknet.js`, we first need to determine whether the user's wallet is a Starknet wallet or an EVM wallet. We can do this by checking selectedAccount from get-starknet is a EVM Wallet or Starknet Wallet. You can distinguish EVM wallets by checking if the window object includes the methods `send` and `sendAsync`, which are not present in Starknet wallets.
Ensure the `node` variable points to a valid Rosettanet node.

```jsx
import { RosettanetAccount, WalletAccount } from "starknet";

const userWalletAccount = await (typeof selectedAccount.sendAsync ===
  "function" && typeof selectedAccount.send === "function"
  ? RosettanetAccount.connect({ nodeUrl: node }, selectedAccount)
  : WalletAccount.connect({ nodeUrl: node }, selectedAccount));
```

> This detection method will be available in the rosettanetjs npm library for easier usage in the near future.

## Sending Requests

Once connected, the DApp can send transactions and requests through the user's wallet. Use the `execute()` method from `starknet.js` to send transactions. `RosettanetAccount` supports nearly all `WalletAccount` methods, except for `declare()` and `deploy()`.

```jsx
const response = await userWalletAccount.execute(StarknetCalldata);
```

If your DApp needs the wallet address, `userWalletAccount.address` will return the address. For EVM wallets, this will be an Ethereum address. You can call a helper contract to obtain the corresponding Starknet address from the Ethereum address:

```jsx
import { RpcProvider, Contract } from "starknet";

export async function getStarknetAddress(address) {
  const starknetProvider = new RpcProvider({
    nodeUrl: nodeUrl,
  });

  const rosettanetContractAddress = "Rosettanet Contract Address";

  const { abi: rosettanetContractAbi } = await starknetProvider.getClassAt(
    contractAddress
  );
  if (contractAbi === undefined) {
    throw new Error("no contract abi, check contract address");
  }
  const rosettanetContract = new Contract(
    rosettanetContractAbi,
    rosettanetContractAddress,
    starknetProvider
  );

  const starknetAddress =
    await rosettanetContract.get_starknet_address_with_fallback(address);
  return "0x" + starknetAddress.toString(16);
}
```

> This method will also be included in the rosettanetjs npm library for convenience.

Rosettanet Account can get information both Ethereum and Starknet requests. For example `blockNumberRosettanet` uses `eth_blockNumber` method and returns Starknet block number, `getBlockNumber` uses `starknet_blockNumber` method and returns Starknet block number.

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.blockNumberRosettanet();

const starknetMethod = await userWalletAccount.getBlockNumber();
```

## Available Ethereum Requests

These methods are using Ethereum requests but retrieve data from Starknet Chain.

### sendTransactionRosettanet

Sends transaction request to wallet. Uses `eth_sendTransaction` method.

Parameters:

| Name   | Description                       | Type                        |
| ------ | --------------------------------- | --------------------------- |
| params | Valid Ethereum Transaction Object | Ethereum Transaction Object |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.sendTransactionRosettanet(
  params
);
```

### chainIdRosettanet

Request the current chain ID from the wallet. Uses `eth_chainId` method.

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.requestChainId();
```

### blockNumberRosettanet

Request latest block number in Starknet. Uses `eth_blockNumber` method.

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.blockNumberRosettanet();
```

### callRosettanet

Call request. Uses `eth_call` method.

Parameters:

| Name | Description                       | Type                        |
| ---- | --------------------------------- | --------------------------- |
| tx   | Valid Ethereum Transaction Object | Ethereum Transaction Object |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.callRosettanet(tx);
```

### estimateGasRosettanet

Estimated gas fee for the transaction. Uses `eth_estimateGas` method

Parameters:

| Name | Description                       | Type                        |
| ---- | --------------------------------- | --------------------------- |
| tx   | Valid Ethereum Transaction Object | Ethereum Transaction Object |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.estimateGasRosettanet(tx);
```

### gasPriceRosettanet

Latest gas price in network. Uses `eth_gasPrice` method.

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.gasPriceRosettanet();
```

### getBalanceRosettanet

STRK balance of given address. Uses `eth_getBalance` method.

Parameters:

| Name    | Description                       | Type   |
| ------- | --------------------------------- | ------ |
| address | Ethereum Address to check balance | string |
| block   | Block number or hash (optional)   | string |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.getBalanceRosettanet(
  address,
  block
);
```

### getBlockByHashRosettanet

Block by given block hash. Uses `eth_getBlockByHash` method.

Parameters:

| Name       | Description                      | type    |
| ---------- | -------------------------------- | ------- |
| blockHash  | Ethereum Block hash.             | string  |
| hydratedTx | Hydrated transactions (optional) | boolean |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.getBlockByHashRosettanet(
  blockHash,
  hydratedTx
);
```

### getBlockByNumberRosettanet

Block by given block number. Uses `eth_getBlockByNumber` method.

Parameters:

| Name        | Description                      | type    |
| ----------- | -------------------------------- | ------- |
| blockNumber | Block number or block tag.       | string  |
| hydratedTx  | Hydrated transactions (optional) | boolean |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.getBlockByHashRosettanet(
  blockNumber,
  hydratedTx
);
```

### getBlockTransactionCountByHashRosettanet

Transaction count of given block hash. Uses `eth_getBlockTransactionCountByHash` method.

Parameters:

| Name      | Description | type   |
| --------- | ----------- | ------ |
| blockHash | Block hash  | string |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.getBlockByHashRosettanet(
  blockHash
);
```

### getBlockTransactionCountByNumberRosettanet

Transaction count of given block number. Uses `eth_getBlockTransactionCountByNumber` method.

Parameters:

| Name        | Description  | type   |
| ----------- | ------------ | ------ |
| blockNumber | Block number | string |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod =
  await userWalletAccount.getBlockTransactionCountByNumberRosettanet(
    blockNumber
  );
```

### getTransactionCountRosettanet

Transaction count of given address. Uses `eth_getTransactionCount` method.

Parameters:

| Name    | Description      | type   |
| ------- | ---------------- | ------ |
| address | Ethereum address | string |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.getTransactionCountRosettanet(
  address
);
```

### getTransactionReceiptRosettanet

Transaction receipt of given transaction hash. Uses `eth_getTransactionReceipt` method.

Parameters:

| Name   | Description               | type   |
| ------ | ------------------------- | ------ |
| txHash | Ethereum transaction hash | string |

```jsx
const userWalletAccount = await RosettanetAccount.connect(
  { nodeUrl: node },
  selectedAccount
);

const ethereumMethod = await userWalletAccount.getTransactionReceiptRosettanet(
  txHash
);
```
