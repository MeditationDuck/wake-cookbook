# ERC-20 Token Fuzzing

This guide covers fuzzing strategies specifically for ERC-20 token contracts, with emphasis on upgradable and pausable implementations.

## Basic Setup

```python
from wake.testing import *
from wake.testing.fuzzing import *
from pytypes.contracts.UpgradablePausableSimpleToken import UpgradablePausableSimpleToken
from pytypes.tests.helpers.import import SimpleERC1967Proxy

class ERC20FuzzTest(FuzzTest):
    token_impl: UpgradablePausableSimpleToken
    token: UpgradablePausableSimpleToken
    proxy: SimpleERC1967Proxy

    def pre_sequence(self) -> None:
        # Deploy implementation
        self.token_impl = UpgradablePausableSimpleToken.deploy(from_=default_chain.accounts[0])

        # Deploy proxy
        init_data = self.token_impl.initialize.encode_input(
            "Test Token", "TST", 18, 1_000_000 * 10**18
        )
        self.proxy = SimpleERC1967Proxy.deploy(
            self.token_impl.address, init_data, from_=default_chain.accounts[0]
        )

        # Create proxy interface
        self.token = UpgradablePausableSimpleToken(self.proxy.address)
```

## Key Invariants

```python
@invariant(period=5)
def total_supply_constant(self):
    """Total supply should remain constant unless minting/burning occurs"""
    # Store initial supply in a global context
    if not hasattr(self, "_initial_supply"):
        self._initial_supply = self.token.totalSupply()

    # Check supply hasn't changed
    assert self.token.totalSupply() == self._initial_supply

@invariant()
def balances_sum_equals_total_supply(self):
    """Sum of all balances should equal total supply"""
    total_balance = 0
    for account in self.accounts:
        total_balance += self.token.balanceOf(account)
    assert total_balance == self.token.totalSupply()
```

## Fuzz Actions

```python
@flow(weight=10)
def transfer_flow(self):
    """Test transfer functionality"""
    sender = random_account()
    receiver = random_account(exclude=[sender])
    amount = random.randint(0, self.token.balanceOf(sender))

    sender_balance_before = self.token.balanceOf(sender)
    receiver_balance_before = self.token.balanceOf(receiver)

    tx = self.token.transfer(receiver, amount, from_=sender)

    # Verify balances
    assert self.token.balanceOf(sender) == sender_balance_before - amount
    assert self.token.balanceOf(receiver) == receiver_balance_before + amount

@flow(weight=5)
def approve_and_transfer_from_flow(self):
    """Test approve and transferFrom functionality"""
    owner = random_account()
    spender = random_account(exclude=[owner])
    receiver = random_account()

    # Only approve amounts the owner actually has
    owner_balance = self.token.balanceOf(owner)
    amount = random.randint(0, owner_balance)

    # Approve
    self.token.approve(spender, amount, from_=owner)

    # TransferFrom
    self.token.transferFrom(owner, receiver, amount, from_=spender)

    # Verify allowance is reduced
    assert self.token.allowance(owner, spender) == 0
```

## Upgradability Testing

```python
@flow(weight=3)
def upgrade_contract_flow(self):
    """Test contract upgrade functionality"""
    if random.random() < 0.5:
        return  # Only upgrade sometimes

    # Deploy new implementation
    new_impl = UpgradablePausableSimpleToken.deploy(from_=default_chain.accounts[0])

    # Record state before upgrade
    name_before = self.token.name()
    symbol_before = self.token.symbol()
    total_supply_before = self.token.totalSupply()

    # Perform upgrade
    self.token.upgradeTo(new_impl.address, from_=default_chain.accounts[0])

    # Verify state is preserved
    assert self.token.name() == name_before
    assert self.token.symbol() == symbol_before
    assert self.token.totalSupply() == total_supply_before
```

## Pause Functionality Testing

```python
@flow(weight=2)
def pause_unpause_flow(self):
    """Test pausing and unpausing functionality"""
    admin = default_chain.accounts[0]  # Assuming account 0 has admin rights

    # Toggle pause state
    if not self.token.paused():
        self.token.pause(from_=admin)
        assert self.token.paused()

        # Try to transfer while paused (should revert)
        sender = random_account()
        if self.token.balanceOf(sender) > 0:
            receiver = random_account(exclude=[sender])
            amount = random.randint(1, self.token.balanceOf(sender))
            with must_revert():
                self.token.transfer(receiver, amount, from_=sender)
    else:
        self.token.unpause(from_=admin)
        assert not self.token.paused()
```

## How to Run

Execute the fuzzing test with this command:

```bash
wake fuzz tests/fuzz_erc20.py --steps 1000 --workers 4
```
