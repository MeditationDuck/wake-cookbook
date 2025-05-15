# Library deployment

Library can be deploy same as common contract in wake test

```python
def test_deploy():
    lib = Library.deploy()

    # library address
    lib.address
```

```sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;

library Library {
    function add(uint256 a, uint256 b) public pure returns(uint256){
        return a + b;
    }
}
```
