# sample code Redis

- open connection
```python
from redis import Redis

# Redis connection
REDIS_HOST = "0.0.0.0"
REDIS_PORT = 32768
REDIS_DB = 0

store = Redis(host=REDIS_HOST, port=REDIS_PORT, db=REDIS_DB)
```

- set data
```python
store.set(KEY, VALUE)
```

- get data
```python
store.set(KEY)
```