# Address/Bytes Conversion Testing

Conversion between addresses and bytes with random data, including validation of byte length and error handling.

```solidity
contract AddressBytesUtils {
    function toAddress(bytes memory b) public pure returns (address) {
        if (b.length != 20) {
            revert("Invalid length");
        }
        assembly { addr := mload(add(b, 20)) }
    }
}
```

```python
utils: AddressBytesUtils = AddressBytesUtils.deploy()

length = random_int(0, 20)
b = random_bytes(length)
with may_revert("Invalid length") as e:
    a = utils.toAddress(b)
    assert a == Address(b.hex())

if e is not None:
    assert e.value == "Invalid length"
```
