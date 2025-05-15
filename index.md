# Wake Cookbook

Welcome to the Wake Cookbook - a guide to testing patterns for smart contracts using Wake. This cookbook is organized into categories from fundamental to specialized patterns. and describes wake grammar.

!!! warning "Important"
This cookbook is a work in progress. While it may provide you with some useful patterns, it may not cover all the use cases.

Running wake test by `$ wake test {path}` for both unit test and fuzz test.
You must run and verify the test behavior for each test generation.
Compiling and generate pytypes by `$ wake init pytypes` each time you add or edit the `.sol` file.
You can only edit the `/tests` directory. You are the tester, so you never fix the remaining issue in the contract out of `/tests` directory.

When you adding mock contract you must add in `/tests/helpers/` directory. you can directory add /tests/helpers/\*.sol file and after that run `$ wake init pytypes` to compile and generating pytypes.

## Importing specification

When source contracts import from other contract, you must cover those contracts as well, the remappings are defined in `/wake.toml` file.
You are testing the contract. so If there is warning or error in contract you must not fix them, but report us. you can only add and change content in `/tests` directory.
To see inheritance relation, look `Inheritance relation and path printer` and run the wake printer.

## Report

When user asked the comprehensive test, after the end of the your development of the test, you must write `tests/report.md` by appending new text. you must report the weird behavior of the contract and error you find in the contract there. you do not write any recommendation.

# Testing library

Especially for `abi.encode` and random library, always use from `from wake.testing import *`.
Detail usage of them are in `wake` documents.

# Categories

User must ask either fuzzing or unit test. Look one of specification.

- [Essential Fundamentals](essential-fundamentals/index.md)
- [Specialized Use Cases](specialized-use-cases/index.md)
- [Fuzzing Specification](fuzzing-patterns/index.md)
- [Unit Test Specification](unit-test-specification/index.md)
- [Inheritance relation and path printer](inheritance-relation/index.md)
