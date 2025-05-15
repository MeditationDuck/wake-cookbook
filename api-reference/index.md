# Wake API Reference

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

## Direct State Manipulation with mint_erc20

For all unit tests involving ERC20 tokens, prefer the `mint_erc20(contract,to,amount)` function over `transfer`s when setting up token balances. This approach is cleaner and more appropriate for unit testing as it:

1. Directly manipulates the token's storage state
2. Bypasses transfer restrictions that might exist in the token contract and totalsupply
3. Reduces test complexity by avoiding unnecessary transactions
4. Makes tests more deterministic and focused on the behavior being tested

Example:

```python
mint_erc20(
    contract=token, ## ERC20 Token contract
    to=player, ## user who increase balance
    amount=amount
)
```

Similarly, use `mint_erc721` and `mint_erc1155` for NFT-related tests when appropriate.

## Debugging & Troubleshooting

Contract function calls return a `TransactionAbc` object for debugging.
`tx.events` is all emitted events, you can use `print(tx.events)`.
`print(tx.call_trace)` call trace of complete execution flow.
`print(tx.console_logs)` console log, debug output during execution

```python
tx = contract.someOperation()
```

### Using Console Logs

You can add `import "wake/console.sol";` in the `.sol` and
By placing `console.log(variable)` or `console.logBytes(bytesData)`. and recompile by `$ wake init pytypes`.
Now available in `tx.call_trace`.

# Timestamp

You can access current timestamp by `chain.blocks["latest"].timestamp`.

# Timestamp manipulation

Example of advancing time on the test. for each chain, test can advance timestamp.

```python
seconds = 10
chain.mine(lambda x: x + seconds)
```
