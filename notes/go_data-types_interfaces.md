# Interfaces in Go

## Introduction to Interfaces in Go

> Interfaces are a type of [collection](go_data-types_collection.md) in Go

An `interface` defines a set of method signatures (input / output) that a data type or struct can implement

- Interfaces provide a way to achieve polymorphism and abstraction
- A type implicitly implements an interface if it has methods matching the interface's signature

> Zero values for interface types (`interface{}`) is `nil`

## Defining an Interface

```go
type <Interface Name> interface {
    // method signatures
}
```

> NOTE: Conventionally, interfaces with one method should end with `-er`

## References / Resources

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [codecademy - interfaces](https://www.codecademy.com/resources/docs/go/interfaces)
