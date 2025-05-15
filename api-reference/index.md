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
Detail in [Signing EIP712 Signatures](permit-functions-with-eip712-signatures.md)

## ABI Encoding

Use correct abi encoding and decoding library from `from wake.testing import abi`.
Use `abi.encode()`, `abi.encode_packed()`, `abi.decode()`.
You can add directly to `abi.encode()` data, without adding defining types.
`abi.decode` is defined as `decode(data: bytes, types: Sequence[Type])`
For example,

```python
data = abi.encode(string("chara data"), account, sender, value, data)
decoded_data = abi.decode(return_data, [str, Address, Address, uint256, bytes])
```
