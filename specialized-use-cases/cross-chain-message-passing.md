# Cross-Chain Message Passing

Example of testing cross-chain message passing between two chains.
The test can add more chain to test cross-chain protocol. for deployment of contract, tester can specify chain at the argument of deployment.
for each chain, the chain belogning account can interact with contract, otherwise it is mandatory to specify the chain at the argument.

```python
chain1: Chain = Chain()
chain2: Chain = Chain()
service1: Service = Service.deploy(chain=self.chain1)
service2: Service = Service.deploy(chain=self.chain2)
sender = random_account(chain=self.chain1)
recipient = random_account(chain=self.chain2)
```
