# Type Aliases in Go

## Intro to Type Aliases

`Type aliases` in Go allow you to create new names for existing types.

- They are useful for improving code readability, adding context to types, or creating shorter and more convenient names for complex types.
- Type aliases do not create new distinct types but provide an alternative name for an existing type.

## Defining Type Aliases

```go
type <AliasName> = <ExistingType>
```

```go
type UserId int
type Direction byte
type Speed float64
type Velocity Speed
```

## Type Conversions

Converting data types is typically done with type aliases.

```go
type UserId int
type Speed float64

// Convert number 5 (int) into UserId type
UserId(5)

// Convert number 88.3 (float) into Speed type
Speed(88.3)
```

```go
// Define custom types for float64
type Celsius float64
type Fahrenheit float64

func main() {
    // Convert float numbers into Celsius and Fahrenheit types
    temperatureC := Celsius(25.0)
    temperatureF := Fahrenheit(77.0)

    fmt.Printf("%.2f°C is equal to %.2f°F\n", temperatureC, temperatureF)
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
