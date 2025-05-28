# IRosettanetAccount

```rust
pub struct RosettanetCall {
    pub tx_type: u8, // 0: Legacy, 1: Eip2930, 2: Eip1559
    pub to: EthAddress,
    pub nonce: u64,
    pub max_priority_fee_per_gas: u128,
    pub max_fee_per_gas: u128,
    pub gas_price: u128, 
    pub gas_limit: u64,
    pub value: u256,
    pub calldata: Span<u128>
}

#[starknet::interface]
pub trait IRosettaAccount<TState> {
    fn __execute__(ref self: TState, call: RosettanetCall) -> Array<Span<felt252>>;
    fn __validate__(self: @TState, call: RosettanetCall) -> felt252;
    fn is_valid_signature(self: @TState, hash: u256, signature: Array<felt252>) -> felt252;
    fn supports_interface(self: @TState, interface_id: felt252) -> bool;
    fn __validate_declare__(self: @TState, class_hash: felt252) -> felt252;
    fn __validate_deploy__(
        self: @TState,
        class_hash: felt252,
        contract_address_salt: felt252,
        eth_address: EthAddress,
        registry: ContractAddress,
    ) -> felt252;
    fn get_ethereum_address(self: @TState) -> EthAddress;
    fn rosettanet(self: @TState) -> ContractAddress;
    fn native_currency(self: @TState) -> ContractAddress;
    fn nonce(self: @TState) -> u64;
    fn isValidSignature(self: @TState, hash: u256, signature: Array<felt252>) -> felt252;
    fn getEthereumAddress(self: @TState) -> EthAddress;
    fn nativeCurrency(self: @TState) -> ContractAddress;
}
```
