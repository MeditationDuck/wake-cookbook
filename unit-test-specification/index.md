This is basic structore for wake unit test

You are given contracts. if not raise it and ask us which contract to be tested.
You must test only source known test. for example if the contract is some vault, but If you can not find `deposit()` public function, you must not write test about it. and ask us, the source code then you can test.

## Testing specification

When you adding mock contract you must add in /tests/helpers/ directory. you can directory add /tests/helpers/\*.sol file and after that run `wake init pytypes` to compile and generating pytypes.

Test can be run by `wake test tests/test_token.py` that specifying path of test.

When your test failed then consider the reason, and if it potencially has issue in the contract, you must report to us.

If there revert is defined, you must also cover by using `must_revert as e` so we can expect the transaction fails. and verify the error content.

```py
import logging
from collections import defaultdict
from collections.abc import Sequence
from dataclasses import dataclass
from typing import override, TypeVar, cast
from ordered_set import OrderedSet
import inspect
import traceback

from wake.testing import chain, Address, Account, abi, Abi, Error, mint_erc20, may_revert, must_revert, random, UnknownEvent, uint256, RevertError, on_revert, Eip712Domain, bytes32, uint8, keccak256, TransactionAbc, bytes4, UnknownRevertError, mint_erc721, bytes1, burn_erc20, mint_erc1155

from wake.development.core import Contract
## this test only support wake-rs
from wake_rs import Chain, default_chain


from pytypes.contracts.SimpleToken import SimpleToken

#library at node_modules/@openzeppelin/contracts/token/ERC20/ERC20.sol";
from pytypes.openzeppelin.contracts.token.ERC20.ERC20 import ERC20

# Print failing tx call trace
# def revert_handler(e: RevertError):
#     if e.tx is not None:
#         print(e.tx.call_trace)


@chain.connect()
# @on_revert(revert_handler)
def test_token_deploy():

    owner = chain.accounts[0]

    token = SimpleToken.deploy(1000000 * 10**18, from_=owner)

    assert token.name() == "SimpleToken"
    assert token.symbol() == "STK"
    assert token.decimals() == 18
    assert token.totalSupply() == 1000000 * 10**18
    assert token.balanceOf(owner) == 1000000 * 10**18

@chain.connect()
def test_token_transfer():
    owner = chain.accounts[0]
    alice = chain.accounts[1]

    token = SimpleToken.deploy(1000000 * 10**18, from_=owner)

    tx = token.transfer(alice, 10 * 10**18, from_=owner)


    events = [e for e in tx.events if isinstance(e, SimpleToken.Transfer)]
    assert len(events) == 1
    event = events[0]
    assert event.from_ == owner.address
    assert event.to == alice.address
    assert event.value == 10 * 10**18

    assert token.balanceOf(alice) == 10 * 10**18
    assert token.balanceOf(owner) == 999990 * 10**18

@chain.connect()
def test_token_transfer_insufficient_balance():
    owner = chain.accounts[0]
    alice = chain.accounts[1]
    initial_supply = 1000000 * 10**18
    transfer_amount = initial_supply + 1  # More than the total supply

    token = SimpleToken.deploy(initial_supply, from_=owner)

    with must_revert() as e:
        token.transfer(alice, transfer_amount, from_=owner)

    ## must check as error type and arguments
    assert e.value == ERC20.ERC20InsufficientBalance(owner.address, initial_supply, transfer_amount)


@chain.connect()
def test_token_approve():
    owner = chain.accounts[0]
    alice = chain.accounts[1]

    token = SimpleToken.deploy(1000000 * 10**18, from_=owner)

    tx = token.approve(alice, 10 * 10**18, from_=owner)

    events = [e for e in tx.events if isinstance(e, SimpleToken.Approval)]
    assert len(events) == 1
    event = events[0]
    assert event.owner == owner.address
    assert event.spender == alice.address
    assert event.value == 10 * 10**18

    assert token.allowance(owner, alice) == 10 * 10**18
```
