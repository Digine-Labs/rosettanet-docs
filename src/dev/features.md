## Using multicall with Rosettanet

As Rosettanet aims to provide EVM experience on Starknet, we tried our best to not compromising from Starknet native features. Rosettanet account contracts supports multicall transactions, raw Starknet calldata transaction and upgrades. We call this type of calls to account contracts as feature calls.

### Sending transaction with raw starknet calldata

If you re willing to send a transaction to interact with a contract that has starknet specific type (such like felt252) as input parameter. We recommend to send this transaction as a multicall transaction. Multicall usage will be explained below.

### Sending multicall transaction

Rosettanet account contracts will act differently depends on target parameter of calldata. Also our account abstractions interface is different than actual Starknet accounts. Please check our [Account interface](../account-interface.md). 

To interact with account contracts features, your transaction target (to address) must be 0x4645415455524553. This address may be changed before Alpha release. However, you can find latest address [here](https://github.com/Digine-Labs/rosettacontracts/blob/27b7b759cf2391b7db56d67ce6be1a1610a4761c/src/constants.cairo#L2).

The calldata of multicall transaction must be encoded like calling multicall((uint256,uint256,uint256[])[]) function in EVM. We recommend to use our [prepareMulticallCalldata](https://github.com/Digine-Labs/rosettanetjs/blob/24d8015fa90fc4676d0773bcd1d6599134f38d34/src/multicall.ts#L4) function in RosettanetJS.

Once you broadcast this transaction. Your calls will be executed in the same order.

### Sending upgrade transaction

Rosettanet account contracts can be upgraded to latest Rosettanet class, which is received from Rosettanet registry. To achieve this all you need to do is, sending a transaction with to address as our feature address (0x4645415455524553), and 0x74d0bb9d as calldata (upgrade selector)