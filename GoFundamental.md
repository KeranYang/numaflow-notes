### 1. How to ask a project to depend on the latest commit of a dependency pkg?

```bash
go get github.com/someone/some_module@af044c0995fe

// e.g.
go get github.com/numaproj/numaflow-go@44ff06c
```

Reference: https://stackoverflow.com/questions/53682247/how-to-point-go-module-dependency-in-go-mod-to-a-latest-commit-in-a-repo

### A trick to ensure an object implements an interface

Sometimes you see it at the top of a go file and you think: "Why do we put it on this line?" It's because we want to explicitly ensure this struct `client` is an implementation of the interface `Client`.

```go
var _ Client = (*client)(nil)
```

