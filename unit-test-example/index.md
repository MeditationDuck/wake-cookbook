# Unit Text Example

```py
import logging

from wake.testing import chain, Address, Account, abi, Abi, Error, mint_erc20, may_revert, must_revert, random, UnknownEvent, uint256, RevertError, on_revert, Eip712Domain, bytes32, uint8, keccak256, TransactionAbc, bytes4, UnknownRevertError, mint_erc721, bytes1, burn_erc20, mint_erc1155
from wake_rs import Chain

from pytypes.contracts.SimpleToken import SimpleToken
from pytypes.openzeppelin.contracts.token.ERC20.ERC20 import ERC20

@chain.connect()
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
```
