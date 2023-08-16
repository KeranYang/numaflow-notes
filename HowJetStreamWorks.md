### Reference

https://github.com/numaproj/numaflow/tree/main/pkg/isb/stores/jetstream

### Reader

1. `Read`

Use the `*nats.Subscription` to fetch N messages.

For each message, create a new offset and then return the message to numaflow data forwarder.

When creating the offset, in addition to assigning sequence number, message payload and partition index, the `newOffset` call also starts a `workInProgress` goroutine.

`workInProgress` tells the jetstream server that this message is being worked on and resets the redelivery timer on the server, every tickDuration.

> Why do we need NoAck()?

Now, if a JetStream message is not acked, the `workInProgress` will hang forever. This was an issue on data forwarder, when we return early during `forwardAChunk` without acknowledging the batch.

To solve the issue, an NoAck() method was added to JetStream offset struct, to proactively cancel the context, hence stops the `workInProgress` gorountine. The NoAck also asks the server to redeliver the corresponding message.

### MISC

1. How is read/writer created?

   `buildJetStreamBufferIO` is only used by **MAP** and **REDUCE** to create a list of JetStream BufferReaders and JetStream BufferWriters.

   It uses a Nats Client Pool to grab the next available client and uses it to interact with JetStream.

2. How does a source vertex interact with JetStream? 

   For source, when we initialize a SourceProcessor, we only create BufferWriters. There is **NO** interaction with JetStream regarding **READING**.

3. Do we need NoAck for a source vertex? 

   According to Read section above, **NO**.





