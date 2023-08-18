### Watermark KV store

A physical data store to save watermarks. The KV store holds two pieces of information: offset timelines and heartbeat timelines.

A watermark kv store **can close itself**. And **only itself can close itself.**

### Processor manager

ProcessorManager manages the point of view of Vn-1 from Vn vertex processors (or source processors). The code is running on the Vn vertex. It has the mapping of all the processors which in turn has all the information about each processor's timelines.

When we create a processor manager, we create KV store watchers for it to track watermarks.

When a processor manager stops, it only closes the kv watermark watchers,  it **DOES NOT** delete the KV stores(neither OT or Heartbeat).

### Watermark fetcher

A fetcher doesn't implement Close() because it doesn't own the KV stores.

### Watermark publisher

The publisher publishes watermarks to kv store, but it doesn't own the kv store because the store can be shared by multiple publishers.

**When a publisher stops**, it only deletes the corresponding keys from the store, it **DOES NOT** delete the KV stores(neither OT or Heartbeat).

### Who owns the Watermark KV store?

**SourceProcessor**, **MapUDFProcessor**, **ReduceUDFProcessor** and **Sinker**. When they start, they create the physical stores for the watermark. BuildProcessorManagers doesn't create kv stores. It just points to the corresponding buckets. BuildPublishersFromStores doesn't create kv stores. It just points to the corresponding buckets.

### Who can create processor managers?

ISBService can create processor managers.

```
CreateProcessorManagers(ctx context.Context, bucketName string, partitions int, isReduce bool) ([]*processor.ProcessorManager, error)
```

JetStream can also create processor managers.

`func BuildProcessorManagers(ctx context.Context, vertexInstance *v1alpha1.VertexInstance, client *jsclient.NATSClient) (map[string]*processor.ProcessorManager, error)`







