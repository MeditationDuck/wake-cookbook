# Wake Testing Document

Welcome to the Wake Testing Document - a guide to testing patterns for smart contracts using Wake.

Running wake test by `$ wake test {path}` for both unit test and fuzz test.
You must run and verify the test behavior for each test generation.
Compiling and generate pytypes by `$ wake init pytypes` each time you add or edit the `.sol` file.
Through pytypes, you can call `public` or `external` functions. When function attribute does not found in the test, regenerate pytypes.
You can only edit and add the `/tests` directory other than "console log". You are the tester, so you never fix the remaining issue in the contract out of `/tests` directory.

When you adding mock contract you must add in `/tests/helpers/` directory.
You can directory add `/tests/helpers/\*.sol` file and run `$ wake init pytypes` to compile and generating pytypes.

Follow [API References](api-reference/index.md) for always.

## Importing Reference

When source contracts import from other contract, you must cover those contracts as well, the remappings are defined in `/wake.toml` file.
You are testing the contract. so If there is warning or error in contract you must not fix them, but report us. you can only add and change content in `/tests` directory.
To see inheritance relation, look [Inheritance relation and path printer](inheritance-relation/index.md) and run the wake printer.

# Testing library

This is basic library list.
Copy and paste this at beginning.

```py
import logging

from wake.testing import chain, Address, Account, abi, Abi, Error, mint_erc20, may_revert, must_revert, random, UnknownEvent, uint256, RevertError, on_revert, Eip712Domain, bytes32, uint8, keccak256, TransactionAbc, bytes4, UnknownRevertError, mint_erc721, bytes1, burn_erc20, mint_erc1155

from wake_rs import Chain
```

Especially for `abi.encode` and random library, always use `wake.testing.abi`.
Details in [API References](api-reference/index.md)

# Categories

User must ask either fuzzing or unit test. Look one of specification.

- [API References](api-reference/index.md)
- [Inheritance relation and path printer](inheritance-relation/index.md)
- [Unit Test Example](unit-test-example/index.md)
