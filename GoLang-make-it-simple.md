## GoLang - Make it simple

1. ##### Multiple independent assignments in one line.

Before

```go
func SumProductDiff(i, j int)(s int, p int, d int) {
    s = i+j
    p = i*j
    d = i-j 
    return
}
```

After

```go
func SumProductDiff(i, j int)(s int, p int, d int) {
    s, p, d = i+j, i*j, i-j
    return
}
```

2. ##### Multiple input/return parameters with the same type.

Before

```go
func SumProductDiff(i int, j int)(s int, p int, d int) {
    s, p, d = i+j, i*j, i-j
    return
}
```

After

```go
func SumProductDiff(i, j int)(s, p, d int) {
    s, p, d = i+j, i*j, i-j
    return
}
```

3. **Force using of the factory of structs instead of `new()`**

```go
package matrix
type matrix struct {
  ...
}
function NewMatrix(params) *matrix {
  m := new(matrix)
  // m is initialized

  return m
}
```

Because of the lower case `m` of `matrix`, we need to use the factory method in another package.

```go
package main
import "matrix"
...
wrong := new(matrix.matrix) // will NOT compile (the struct matrix can't be used directly)
right := matrix.NewMatrix(...) // the ONLY way to instantiate a matrix
```
