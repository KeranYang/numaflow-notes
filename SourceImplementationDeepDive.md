# Source Implementation Deep Dive

### A source needs to implement the following methods

#### StarterStopper

```go
// StarterStopper starts/stops the forwarding.
type StarterStopper interface {
	Start() <-chan struct{}
	Stop()
	ForceStop()
}
```

##### Start

It starts the forwarder.

##### Stop

It stops the forwarder.

##### ForceStop

It forces stopping the forwarder.

#### Closer

##### Close

1. calls cancel function
2. closes any opening clients



### Ack

Kafka: `return make([]error, len(offsets))`

Generator: `return make([]error, len(offsets))`

HTTP: `return make([]error, len(offsets))`

Nats: `return make([]error, len(offsets))`

RedisStream:

```
if err := br.Client.XAck(RedisContext, br.Stream, br.Group, strOffsets...).Err(); err != nil {
		for i := 0; i < len(offsets); i++ {
			errs[i] = err // 'errs' is indexed the same as 'offsets'
		}
		if br.Metrics.AckErrorsAdd != nil {
			br.Metrics.AckErrorsAdd(len(strOffsets))
		}
	} else {
		if br.Metrics.AcksAdd != nil {
			br.Metrics.AcksAdd(len(strOffsets))
		}
	}
	return errs
```

### User Defined Source

1. When do we call start?

- `NewProcessorCommand` starts the `SourceProcessor`, which starts the `sourcer`

```
udsGRPCClient is started at SourceProcessor, so it should be 
```

