# HowToWriteUDSink

An example UDSink: https://github.com/numaproj/numaflow-sinks/blob/main/http-sink/main.go

Corresponding image: https://quay.io/repository/numaio/numaflow-sink/http-sink

Command Line Args:
```
 -- headers  HTTP Headers 
 -- insecure-skip-tls-verify   Skip TLS verify
 -- method HTTP Method (default "GET")
 -- retries Request Retries (default 3) 
 -- timeout Request Timeout in seconds (default 30)
 -- url URL
```



### How to grab a parameter from the template and pass it to UDSource

