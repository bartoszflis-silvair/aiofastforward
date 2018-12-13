# aiofastfoward

Fast-forward time in asyncio Python by patching [loop.call_later](https://docs.python.org/3/library/asyncio-eventloop.html#asyncio.loop.call_later), [loop.call_at](https://docs.python.org/3/library/asyncio-eventloop.html#asyncio.loop.call_at), [loop.time](https://docs.python.org/3/library/asyncio-eventloop.html#asyncio.loop.time), and [asyncio.sleep](https://docs.python.org/3/library/asyncio-task.html#asyncio.sleep). This allows you to test asynchronous code synchronously.

Inspired by [AngularJS $timeout.$flush](https://docs.angularjs.org/api/ngMock/service/$timeout#flush).

## Usage

### loop.call_later

```python
# Production code
async def schedule_callback(loop, callback):
    loop.call_later(1, callback, 0)
    loop.call_later(2, callback, 1)

# Test code
from unittest.mock import Mock, call
loop = asyncio.get_running_loop()

with aiofastfoward.FastForward(loop) as forward:
    callback = Mock()
    await schedule_callback(loop, callback)

    await forward(1)
    self.assertEqual(callback.mock_calls, [call(0)])
    await forward(1)
    self.assertEqual(callback.mock_calls, [call(0), call(1)])
```


### asyncio.sleep

```python
# Production code
async def sleeper(callback):
    await asyncio.sleep(1)
    await asyncio.sleep(2)
    callback(0)

# Test code
loop = asyncio.get_running_loop()
callback = Mock()

with aiofastforward.FastForward(loop) as forward:
    asyncio.create_task(sleeper())

    await forward(3)
    self.assertEqual(callback.mock_calls, [call(0)])
```
