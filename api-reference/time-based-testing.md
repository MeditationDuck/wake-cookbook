# Time-Based Testing

Example of advancing time on the test. for each chain, test can advance timestamp.

```python
seconds = 10

# This increase `chain.blocks["latest"].timestamp` timestamp.
chain.mine(lambda x: x + seconds)
```
