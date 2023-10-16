# UDSource auto-scaling

### Problem Statement

Before UDSource, Numaflow has the following logic to determine whether to enable auto-scaling for a source vertex.

```go
func (v Vertex) Scalable() bool {
  // ...
	if v.IsASource() {
		src := v.Spec.Source
		if src.Kafka != nil {
			return true
		}
	}
	return false
}
```

It basically says only Kafka source is scalable.

Now that we introduced UDSource, **how do we determine if it's scalable?** And if a UDSource is scalable, **how do we auto-scale it?**



### Solution

#### How do we determine if a UDSource is scalable?

I propose that **if a source vertex is a UDSource, it is scalable.**

#### How do we auto-scale it?

I propose to **continue using the existing auto-scaling logic.**



### Case Study - there are 3 scenarios.

#### Scenario 1

The UDSource implements a **real** `pending()` method. **real** means the method actually contains logic to consult the source and get a real pending count. The count can be positive, 0 and negative. **negative** means the pending information is temporarily unavailable on the server side.

#### Scenario 2

The UDSource doesn't support `pending`, hence it's always returning a negative number. [The numaflow grpc_udsource](https://github.com/numaproj/numaflow/blob/main/pkg/sources/udsource/grpc_udsource.go#L77) will treat it as `isb.PendingNotAvailable`.

#### Scenario 3

There is an error occurring on the server side, hence `pending` returns an error. [The numaflow metric server](https://github.com/numaproj/numaflow/blob/main/pkg/metrics/metrics.go#L179) will treat the error as `isb.PendingNotAvailable` , log the error and continue the program without recording any pending information.

### The 3 scenarios essentially lead to two branches: 1. non-negative pending count and 2. `isb.PendingNotAvailable`

#### branch 1: Non-Negative pending count

* The Metrics Server will build the pending information.
* The vertex is defined as scalable because it's UDSource. The auto-scaler is watching the vertex.
* When scaling
  * The scaler successfully gets a pending count.
  * The scaler calculates `desiredReplicas` and then scales the vertex accordingly. **GOOD.**

#### branch 2: `isb.PendingNotAvailable`

* The Metrics Server will skip building the pending information.
* The auto-scaler watches the vertex.
* When scaling
  * The scaler can't get a pending count.
  * The scaler [doesn't do anything](https://github.com/numaproj/numaflow/blob/main/pkg/reconciler/vertex/scaling/scaling.go#L254). **ALSO GOOD.**

### MISC

* For sources that are NOT scalable, like HTTP, how do we set the replica count?

Numaflow [looks for](https://github.com/numaproj/numaflow/blob/main/pkg/apis/numaflow/v1alpha1/vertex_types.go#L402) the `replicas` specification from the pipeline spec. If not specified, 1.

The controller uses the spec as [desired replicas](https://github.com/numaproj/numaflow/blob/main/pkg/reconciler/vertex/controller.go#L123). So for HTTP, if not specified, the vertex by default has 1 replica and it will never scale up or down.

* If the source vertex ever scaled down to 0, with a UDSource not implementing a real `pending`. Both pending and rate will be nil, in this case, will the vertex ever scale up?

Yes. It will. Because when a vertex has been scaled down to 0 for a certain amount of time. Auto-scaler will [scale it up to 1](https://github.com/numaproj/numaflow/blob/main/pkg/reconciler/vertex/scaling/scaling.go#L210). This happens before the `pending` and `rate` metrics kick in.

* How is the change tested?

Tested with a user-defined Kafka source. Verified that the source vertex can scale.

