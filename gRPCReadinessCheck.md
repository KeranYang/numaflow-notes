# gRPC readiness check

1. At the sourcer side, before numaflow creates the sourcer, it creates the UDTransformer first and then performs the readiness check by calling `transformer.WaitUntilReady()` followed by `defer transformer.CloseConn(ctx)`. This is reasonable because we don't want to start the sourcer without ensuring the transformer gRPC is ready.
2. The question is, we also use the transformer as a health checker for the metric server, how does that work and why?

* The metric server listens on an HTTP port. When receiving the call under path `/sidecar-livez`, it executes a sequence of health checks. Taking the transformer as a health checker, it will call the IsHealthy() API to check the healthiness of the transformer container.
* When will the metric server receive calls to `/sidecar-livez`? When we specify a pod Spec, we define the liveness probe for each of the containers. That's where we specify the periodical probe of the container liveness. The container will be restarted if the probe fails.

3. Current Problem

If **one of** the gRPC servers is not healthy, LivenessProbe for liveness probe for **ALL** containers will fail and all containers will restart. TO BE CONFIRMED with Derek.