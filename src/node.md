# Rosettanet Node

Rosettanet Node is a middleware of the Rosettanet architecture. Main idea behind Rosettanet is making Starknet RPCs work like Ethereums. With that we will be able to use existing libraries and toolings with Starknet. So Rosettanet Node is the main entrypoint for interacting with Starknet as like Ethereum.

## How does it works?

Node simply supports all [JSON-RPC methods](https://ethereum.org/en/developers/docs/apis/json-rpc/) that any public Ethereum RPC supports. You can use Rosettanet endpoint as like Ethereum RPC endpoint. Inputs and outputs will be same as Ethereum. You can track our progress on JSON-RPC methods [here](./methods.md).

## Requirements

Rosettanet Node is not a resource consuming application. Written in Typescript and Rust version will be developed soon. We recommend to run Rosettanet Node on the same server with Starknet RPC running. Rosettanet Node needs to connect a Starknet RPC without any IP or request limit. Using Rosettanet with public Starknet RPCs may cause request limit reaches. Node can make up to 10 RPC calls for a simple eth_call request.
