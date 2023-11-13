### 1. How to ask a project to depend on the latest commit of a dependency pkg?

```bash
go get github.com/someone/some_module@af044c0995fe

// e.g.
go get github.com/numaproj/numaflow-go@44ff06c
```

Reference: https://stackoverflow.com/questions/53682247/how-to-point-go-module-dependency-in-go-mod-to-a-latest-commit-in-a-repo

### 2. A trick to ensure an object implements an interface

Sometimes you see it at the top of a go file and you think: "Why do we put it on this line?" It's because we want to explicitly ensure this struct `client` is an implementation of the interface `Client`.

```go
var _ Client = (*client)(nil)
```

### 3. How to upgrade Go version

https://github.com/numaproj/numaflow/pull/966/files

### 4. The flag library

Within container, we can use flag library to read input configurations from command line.

```go
var prefixStr string
flag.StringVar(&prefixStr, "prefix", "test-payload-", "Prefix of the payload")
flag.Parse()
simpleSource := impl.NewSimpleSource(prefixStr)
```

On Numaflow template, we can declare container as follow:

```yaml
image: quay.io/numaio/numaflow-go/source-simple-source:v0.5.2
args: ["--prefix=abc-"]
```

The UDSource will then use `abc-` instead of default `test-payload-`, as its message payload prefix.

### 5. The % sign

`%d` -> integer value

`%f` -> floating point

`%s` -> plain string

`%v` -> default format

`%w` -> Enabling **WRAPPING** errors

Wrapping an error with `%w` makes it available to `errors.Is` and `errors.As`: (More details in [Working with Errors](https://go.dev/blog/go1.13-errors))

```go
err := fmt.Errorf("access denied: %w", ErrPermission)
...
if errors.Is(err, ErrPermission) ...
```

### 6. go mod

```bash
# Start a new module
go mod init module_name
# Add dependency
go get
# clean up and ensure "go.sum" files are up-to-date
go mod tidy
```

7. How to create a library Golang repo

```bash
go mod init github.com/numaproj-contrib/numaflow-utils-go
```

