# Token testing

You can use `mint_erc20`, `burn_erc20`, `mint_erc721`, `mint_erc1155` wake library in the test, that manipulate storage.

So during test, the original contract does not have access to increase total supply or mint or burn, you can use this functions.
Especially for the unit test, if the testing target is not mint or burn, you should use those function to make the test simple.
