# Nats

1. To start a Nats source, we need the following objects:

* A Nats connection.

```go
// A Conn represents a bare connection to a nats-server.
// It can send and receive []byte payloads.
// The connection is safe to use in multiple Go routines concurrently.
```

* A Nats subscription.

```go
// Subscription represents interest in a given subject.
```

2. When creating a Nats source, we follow the steps below:

* We need to configure `natslib.Option`

  ```go
  natslib.MaxReconnects(-1)
  natslib.ReconnectWait(3 * time.Second)
  natslib.DisconnectHandler(func(c *natslib.Conn) {
  			n.logger.Info("Nats disconnected")
  		})
  natslib.ReconnectHandler(func(c *natslib.Conn) {
  			n.logger.Info("Nats reconnected")
  		})
  // If TLS enabled
  c := TLS CONFIG
  opt = append(opt, natslib.Secure(c))
  // If Auth enabled
  // 1. Basic auth which contains a user name and a password
  opt = append(opt, natslib.UserInfo(username, password))
  // or 2. Token auth
  opt = append(opt, natslib.Token(token))
  // or 3. NKey auth
  opt = append(opt, natslib.NkeyOptionFromSeed(nkeyFile))
  
  // Connect to nats service...
  n.logger.Info("Connecting to nats service...")
  	if conn, err := natslib.Connect(source.URL, opt...); err != nil {
  		return nil, fmt.Errorf("failed to connect to nats server, %w", err)
  	} else {
  		n.natsConn = conn
  	}
  
  // Start the subscription
  if sub, err := n.natsConn.QueueSubscribe(source.Subject, source.Queue, func(msg *natslib.Msg) {
  		readOffset := isb.NewSimpleStringPartitionOffset(uuid.New().String(), vertexInstance.Replica)
  		m := &isb.ReadMessage{
  			Message: isb.Message{
  				Header: isb.Header{
  					// TODO: Be able to specify event time.
  					MessageInfo: isb.MessageInfo{EventTime: time.Now()},
  					ID:          readOffset.String(),
  				},
  				Body: isb.Body{
  					Payload: msg.Data,
  				},
  			},
  			// TODO: Be able to specify an ID for dedup?
  			ReadOffset: readOffset,
  		}
  		n.messages <- m
  	}); err != nil {
  		n.natsConn.Close()
  		return nil, fmt.Errorf("failed to QueueSubscribe nats messages, %w", err)
  	} else {
  		n.sub = sub
  	}
  ```
