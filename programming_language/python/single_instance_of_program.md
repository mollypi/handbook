# Single instance of program

```text
pip install tendo

from tendo import singleton
me = singleton.SingleInstance() # will sys.exit(-1) if other instance is running
```

