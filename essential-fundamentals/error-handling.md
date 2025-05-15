# Error Handling

Example of testing a complex function with multiple branches, including handling errors and ensuring all branches are covered.

```solidity
contract ComplexFunction {
    error InsufficientBalance();
    error InvalidAmount();
    error Unauthorized();

    function complexTransfer(
        address from,
        address to,
        uint256 amount,
        bytes calldata data
    ) external {
        if (amount == 0) revert InvalidAmount();
        if (balances[from] < amount) revert InsufficientBalance();
        if (!isAuthorized(msg.sender)) revert Unauthorized();

        if (data.length > 0) {
            // Complex path 1
            _handleData(data);
            balances[from] -= amount;
            balances[to] += amount;
        } else {
            // Complex path 2
            balances[from] -= amount;
            balances[to] += amount;
        }
    }
}
```

```python

def complex_transfer(self) -> None:
    sender = random_account()
    recipient = random_account()

    # Test invalid amount branch
    with must_revert(ComplexFunction.InvalidAmount) as e:
        complex_contract.complexTransfer(
            sender,
            recipient,
            0,
            b"",
            from_=sender
        )

    # Test insufficient balance branch
    amount = random_int(_balances[sender] + 1, 2**256 - 1)
    with may_revert(ComplexFunction.InsufficientBalance) as e:
        complex_contract.complexTransfer(
            sender,
            recipient,
            amount,
            b"",
            from_=sender
        )
        _balances[sender] -= amount
        _balances[recipient] += amount

    # Test unauthorized branch
    unauthorized = random_account()
    with must_revert(ComplexFunction.Unauthorized) as e:
        complex_contract.complexTransfer(
            sender,
            recipient,
            100,
            b"",
            from_=unauthorized
        )

    # Test successful data path
    amount = random_int(0, _balances[sender])
    data = random_bytes(1, 100)

    complex_contract.complexTransfer(
        sender,
        recipient,
        amount,
        data,
        from_=sender
    )

    _balances[sender] -= amount
    _balances[recipient] += amount
    _validate_data_processed(data)

    # Test successful no-data path
    amount = random_int(0, _balances[sender])
    complex_contract.complexTransfer(
        sender,
        recipient,
        amount,
        b"",
        from_=sender
    )
```

Example of testing complex inputs validation with different error handling scenarios.

```solidity
// Example of complex inputs validation
contract ComplexInputs {
    error InvalidSignature();
    error ExpiredDeadline();
    error InvalidNonce();

    struct ComplexParams {
        address owner;
        uint256 value;
        uint256 nonce;
        uint256 deadline;
        bytes signature;
    }

    function validateAndExecute(ComplexParams calldata params) external {
        if (params.deadline < block.timestamp) revert ExpiredDeadline();
        if (params.nonce != nonces[params.owner]) revert InvalidNonce();
        if (!_verify(params)) revert InvalidSignature();

        // Execute logic
        _execute(params);
    }
}
```

```python

def validate_complex_inputs(self) -> None:
    # Test expired deadline
    params = _generate_valid_params()
    params.deadline = random_int(0, block.timestamp - 1)

    with must_revert(ComplexInputs.ExpiredDeadline):
        complex_input_contract.validateAndExecute(params)

    # Test invalid nonce
    params = _generate_valid_params()
    params.nonce = _nonces[params.owner] + 1

    with must_revert(ComplexInputs.InvalidNonce):
        complex_input_contract.validateAndExecute(params)

    # Test invalid signature
    params = _generate_valid_params()
    params.signature = random_bytes(65)

    with must_revert(ComplexInputs.InvalidSignature):
        complex_input_contract.validateAndExecute(params)

    # Test successful path
    params = _generate_valid_params()
    complex_input_contract.validateAndExecute(params)

    _nonces[params.owner] += 1
    _validate_execution(params)
```
