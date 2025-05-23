# Ethers

Using `ethers.js` with Rosettanet is straightforward, it works just like it does on Ethereum, with only a few small adjustments. Below is a simple example of how to read data from a contract using `ethers.Contract` as you would in any typical Ethereum DApp:

```js
const TestAccount1 = "account-address";
const STRKToken = "token-address";

// Connect to Rosettanet-compatible Starknet RPC
const provider = new ethers.JsonRpcProvider("Rosettanet-RPC-URL");

// A minimal ERC-20 ABI for demonstration purposes
const ERC20_ABI = [
    'function balanceOf(address owner) view returns (uint256)',
    'function decimals() view returns (uint8)'
];

// Instantiate the token contract
const tokenContract = new ethers.Contract(STRKToken, ERC20_ABI, provider);

// Read the token balance
const balance = await tokenContract.balanceOf(TestAccount1);

// Read the token decimals
const decimals = await tokenContract.decimals();
```

This code behaves the same way as it would on Ethereum. The Rosettanet Node takes care of translating the Ethereum-compatible calls to Starknet under the hood. No custom provider wrappers or contract interaction logic is required just plug in your Rosettanet RPC URL and use `ethers` as usual.

## Sending Transactions With `Wallet` Class

When using Rosettanet with `ethers.js`, the only modification required is in how you send transactions using the `Wallet` class.

Unfortunately, calling `wallet.sendTransaction(...)` will not work as expected. This is because `ethers` internally computes the transaction hash and expects the RPC node to return the exact same hash. If there's a mismatch, `ethers` will reject the transaction and throw an error.

Since Rosettanet executes transactions on the Starknet network, it cannot provide the Ethereum-style precalculated transaction hash that `ethers` expects. As a result, direct usage of `sendTransaction` is incompatible.

✅ Recommended Workaround
To send a transaction successfully, you can follow these three steps:

* Populate the transaction using `wallet.populateTransaction(...)`

* Sign the transaction with `wallet.signTransaction(...)`

* Send the raw signed transaction using `provider.send('eth_sendRawTransaction', [signedTx])`

Here’s a full example:

```js
const provider = new ethers.JsonRpcProvider("Rosettanet-RPC-URL");

const privateKey = 'account-priv-key';

// Creating new Wallet class
const wallet = new ethers.Wallet(privateKey, provider);
        
const toAddress = 'to-address';

// We cant directly send transaction with wallet.sendTransaction. 
// Because it expects precalculated tx hash from RPC.
// And it reverts if hash is different.

// Populating Transaction
const txRequest = await wallet.populateTransaction({
    to: toAddress,
    value: ethers.parseEther('1.0')
});

// Signing Transaction
const signedTx = await wallet.signTransaction(txRequest);

// Sending transaction to blockchain network
await provider.send('eth_sendRawTransaction', [signedTx])

// Checking toAddress for incoming funds

// A minimal ERC-20 ABI for demonstration purposes
const ERC20_ABI = [
    'function balanceOf(address owner) view returns (uint256)',
    'function decimals() view returns (uint8)'
];

const STRKToken = "token-address";

// Instantiate the token contract
const strkContract = new ethers.Contract(STRKToken, ERC20_ABI, provider);

// Read the token balance
const balance = await strkContract.balanceOf(toAddress);
```

## Sending Transactions With Browser Wallet (Metamask)

You can send transactions through Browser Wallet using `ethers.js` with little to no modification. Here's a basic example to get started.

This time, we'll use `ethers.BrowserProvider` as the `provider`, since we're interacting with a wallet injected into the browser (like MetaMask). Unlike with `wallet.sendTransaction()`, there's no need for any workaround here you can directly use `signer.sendTransaction()`.

```js
const provider = new ethers.BrowserProvider(window.ethereum);

// Request wallet connection
const signer = await provider.getSigner();

// Now you can send transactions using signer.sendTransaction()
const tx = {
    to:"toAddress",
    value:ethers.parseEther(1) // 1 STRK
};

const txResponse = await signer.sendTransaction(tx);
```

### Transactions with `data`

If your transaction includes a `data` payload, you first need to register your smart contract with the Rosettanet registry. You can do this by calling the `register_contract` function on the [Rosettanet Sepolia smart contract](https://sepolia.voyager.online/contract/0x065a6238502254a31072c53bedf5046cbb626ce49cd49ba20e206b35d5aed0ad#writeContract).

After registration, you can retrieve the corresponding Ethereum-compatible address of your Starknet contract using the `get_ethereum_address` function.

When constructing your transaction:

* The `to` field should be set to the Ethereum address returned from the registry.

* The `data` field must be manually encoded.

To encode the `data` correctly, create a new instance of the `ethers.Interface` class using your contract’s ABI. This allows you to encode function calls as shown below (using a minimal ERC-20 ABI for demonstration):

```js
const provider = new ethers.BrowserProvider(window.ethereum);

const signer = await provider.getSigner();

const ERC20_ABI = [
    'function balanceOf(address owner) view returns (uint256)',
    'function decimals() view returns (uint8)',
    'function transfer(address to, uint256 amount) public returns (bool)'
];

const iface = new ethers.Interface(ERC20_ABI);

const calldata = iface.encodeFunctionData('transfer', [
    'transfer-to-address',
    ethers.parseUnits('1', 18)
]);

const tx = {
    to:"ethereum address from get_ethereum_address()",
    data: calldata,
    chainId:1381192787 // Rosettanet Sepolia Chain Id
};

const txResponse = await signer.sendTransaction(tx);
```

### Using `multicall`

If you want to use `multicall` feature from starknet, you need to pass `to` field as `0x0000000000000000000000004645415455524553`. Below is an example of how to transfer ERC-20 tokens to two different accounts using a `multicall`:

 ```js
const provider = new ethers.BrowserProvider(window.ethereum);

const signer = await provider.getSigner();

const MULTICALL_ABI = [
    'function multicall((uint256,uint256,uint256[])[])'
];

const iface = new ethers.Interface(MULTICALL_ABI);

const receiver1SnAddress = await precalculateStarknetAddress('reciever-1-eth-address');
const receiver2SnAddress = await precalculateStarknetAddress('reciever-2-eth-address');

const data = iface.encodeFunctionData('multicall', [[ 
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
]]);

const tx = {
    to:"0x0000000000000000000000004645415455524553",
    data: calldata,
    chainId:1381192787 // Rosettanet Sepolia Chain Id
};

const txResponse = await signer.sendTransaction(tx);
```