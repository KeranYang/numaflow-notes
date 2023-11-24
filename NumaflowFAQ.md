### Numaflow common errors and fixes

1. ```
   exec /usr/bin/udbx: exec format error
   ```

   Root Cause: It's because I built it in my machine which is arm64, but the cluster is in amd64.

   Fix:

```
build:
	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -v -o osam-apigw-numa .
```

2. ```
   panic: failed to create runtime-env.js file: write /opt/numaflow/runtime-env.js: no space left on device
   ```

   Root Cause: It's because local docker runs out of spaces

   Fix:

```
docker system prune
```

3. ```
   panic: failed to create side inputs KV "dev-devx-o11yabstractgs-use2-qal-o11y-mesh-gs-pipeline_SIDE_INPUTS", nats: replicas > 1 not supported in non-clustered mode
   ```

   Root Cause: ISBSVC is created on a single node mode. It is probably because user creates the ISBSVC by specifying replica = 1.

   Fix: Remove the replica property and re-create.

4. ```
   kafka server: The provider group protocol type is incompatible with the other members
   ```

   Root Cause: Numaflow changed kafka BalanceStrategy from round-robin in v0.8 to range in v1.0.

   Fix: Changing the consumer group name.

5. > Can I have an example pipeline which reads from kafka and writes to kafka?
   
   Yes, take a look at: https://github.intuit.com/oss-analytics/sample-dataflow-deployment/blob/master/environments/prd-usw2-eks/kafka-rw.yaml

6. > What are the recommended/supported jetstream version by numaflow version x.x.x?

   Please refer to https://github.intuit.com/oss-analytics/numaproj/blob/main/docs/numaflow/versions.md








