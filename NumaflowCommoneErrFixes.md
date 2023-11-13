### Numa common errors and fixes

1. ```
   exec /usr/bin/udbx: exec format error
   ```

   Root Cause: It's because I built it in my machine which is arm64, but the cluster is in amd64.

   Fix:

```
build:
	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -v -o osam-apigw-numa .
```



