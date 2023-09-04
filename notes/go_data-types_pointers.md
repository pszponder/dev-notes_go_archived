# Pointers in Go

- A `pointer` type represents memory addresses of variables
- A pointer is a variable that stores the memory address of another type
- Useful for indirect access to values in memory

> Zero value for Pointer Types (`*T`) is `nil`

## Declaring Pointers

```go
// Declaration of a pointer with "*"
var p *int
```

```go
number := 19

// Create a pointer from variable using "&"
p = &number
```

## References / Resources

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
