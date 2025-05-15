# Time-Based Testing

Example of advancing time on the test. for each chain, test can advance timestamp.

```python
seconds_advance = 10

chain.mine(lambda x: x + seconds_advance)
```
