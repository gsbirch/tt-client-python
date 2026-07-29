# Tandem Tales Client (Python)

This library provides the abstract classes
[`tt.Client`][tt.Client] and [`tt.Factory`][tt.Factory] for implementing
[Tandem Tales](https://tandemtales.net) agents in Python.

[`Client`][tt.Client] handles connecting to the
[server](https://github.com/sgware/tt-server) and joining a session. A minimal
subclass of [`Client`][tt.Client] only needs to implement the
[`on_choice`][tt.Client.on_choice] method to choose what the agent will do on
its turn.

[`Factory`][tt.Factory] continuously creates new clients to connect to the
server and wait for partners. A minimal subclass of [`Factory`][tt.Factory] only
needs to implement the [`create`][tt.Factory.create] method to return a new
Client instance.

Here is an example client and factory:
```
import random
import tt

class MinimalClient(tt.Client):
    
    def __init__(self, url, port):
        super().__init__('random', None, None, None, None, None, url, port)
    
    def on_choice(self, status):
        choices = status['choices']
        count = len(choices)
        choice = random.randint(0, count - 1)
        print(choices[choice]['description'])
        return choice

class MinimalClientFactory(tt.Factory):
    
    def create(self):
        return MinimalClient('localhost', 9005)

factory = MinimalClientFactory()
factory.run()
```

Both classes have other lifecycle methods that are meant to be overridden.

[`Client`][tt.Client] has the following lifecycle methods:

- [`on_connect`][tt.Client.on_connect], which runs after it connects to the
  server.
- [`on_start`][tt.Client.on_start], which runs after its session starts.
- [`on_update`][tt.Client.on_update], which runs each time the server sends a
  state update (even if it is not the client's turn).
- [`on_choice`][tt.Client.on_choice], which runs each time the server sends a
  state update and it is the client's turn.
- [`on_end`][tt.Client.on_end], which runs if the story session reaches a
  pre-defined ending.
- [`on_close`][tt.Client.on_close], which runs after the client ends its session
  without raising an exception.
- [`on_stop`][tt.Client.on_stop], which runs after the client's session has
  ended.
- [`on_disconnect`][tt.Client.on_disconnect], which runs after the client has
  disconnected.

[`Factory`][tt.Factory] has the following lifecycle methods:

- [`on_start`][tt.Factory.on_start], which runs after the factory starts.
- [`on_close`][tt.Factory.on_close], which runs after the factory has been shut
  down.
- [`on_stop`][tt.Factory.on_stop], which runs before the factory stops.

This [more complete example](https://github.com/sgware/tt-random-agent-python)
shows you everything you need to download and run a Tandem Tales server and
connect a custom Python agent.