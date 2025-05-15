# Wake API Reference

- [Time Based Testing](time-based-testing.md)
- [Token Testing](token-testing.md) - Wake library for manipulate user token balance.
- [Signing EIP712 Signatures](permit-functions-with-eip712-signatures.md)

## Account

### Address from Account

Default accounts are exist in `chain.accounts` tuple.
`Account` object has `Account.address` which returns `Address` object.
so assert like this `assert some_account.address == some_address`.

### Transaction to Account

Use `Account.transact()` to initialize transaction with manual data and value to account.
This is example when transfer native token to contract from account.

```py
tx = contract.transact(data=b"", value=10**18, from_=chain.account[0])
```

When constructing calldata, use `abi.encode_call()`.
If `Pool` contract has `function placePosition(address user, uint256 amount)` interface, use

```python
call_data = abi.encode_call(pool.placePosition, [user_account, amount_value])
```

### Signing by Account

`Account` object can sign data.
Signing data hash by `account.sign_hash(data=data_hash)`.
For EIP-712 typehash, use `account.sign_structured()`.
Details in "Signing EIP712 Signatures" page.

## ABI Encoding

Use correct abi encoding and decoding library from `from wake.testing import abi`.
Use `abi.encode()`, `abi.encode_packed()`, `abi.decode()`.

### abi.encode()

Syntax: `abi.encode(*args)`

Encodes values according to the Solidity ABI specification, equivalent to Solidity's `abi.encode()`.

Key points:

- Always use explicit type casting for numeric values (uint8, uint32, uint256, etc.)
- No type strings are needed as first arguments (unlike some other encoding libraries)

Example:

```python
# Encoding a struct-like array of tuple of with (uint8 symbol, uint32 numbers)
array_of_tuple = [
    (uint8(1), uint32(12345)),
    (uint8(2), uint32(67890))
]
data = abi.encode(
    round_address,       # Address
    player_address,      # Address
    uint256(2),          # Number of array_of_tuple (explicitly typed)
    "Hello"              # String
    array_of_tuple       # Array of tuples with explicit typing
)
```

### abi.encode_packed()

Use same way as `abi.encode()`

### abi.decode()

For decoding, use `abi.decode(data, [types])` where types are the expected Python types.

`abi.decode` is defined as `decode(data: bytes, types: Sequence[Type])`
For example,

```python
data = abi.encode(str_data, account, sender, value, data)
decoded_data = abi.decode(return_data, [str, Address, Address, uint256, bytes])
```

## Data Types

You can use `uint256`, `uint32`, `uint8`,`bytes32`, `bytes1` and others.

If it has undefined error happen import from `from wake.testing` same as available types.

Solidity to Wake type mapping

- `address` → Address object
- `uint8/uint32/etc.` → uint8(value)/uint32(value)/etc.
- `bytes` → bytes object
- `bytes1/bytes32/etc.` → static length bytes
- `string` → Python string
