# Number Types in Go

## Intro to Number Types in Go

Numbers are a [basic type](go_data-types_basic.md) in Go

There are 3 types of numbers in Go:

- Integers (signed & unsigned)
- Floating-point numbers
- Complex Numbers

## Zero Values for Number Types in Go

> Zero value for integer types is `0`

> Zero value for float types is `0.0`

> Zero value for complex types is `0 + 0i`

## Signed Integer Types

Signed (`int`) integers can be positive or negative

| Data Type | Min Value       | Max Value      |
| --------- | --------------- | -------------- |
| `int8`    | -128            | 127            |
| `int16`   | -3.2,768        | 32,767         |
| `int`     | -2.147483648e+9 | 2.147483647e+9 |
| `int32`   | -2.147483648e+9 | 2.147483647e+9 |
| `rune`    | -2.147483648e+9 | 2.147483647e+9 |
| `int64`   | -9.223372e+18   | -9.223372e+18  |

> `rune` is an alias for `int32`

## Unsigned Integer Types

Unsigned (`uint`) integers can only contain positive numbers

| Data Type | Min Value | Max Value      |
| --------- | --------- | -------------- |
| `uint8`   | 0         | 255            |
| `byte`    | 0         | 255            |
| `uint16`  | 0         | 65,535         |
| `uint`    | 0         | 4.294967296e+9 |
| `uint32`  | 0         | 4.294967296e+9 |
| `uint64`  | 0         | 1.8446744e+19  |
| `uintptr` | 0         | <pointer size> |

> `byte` is an alias for `uint8`

## Floating Point Types

| Data Type    | Description                            |
| ------------ | -------------------------------------- |
| `float32`    | 32-bit floating point                  |
| `float64`    | 64-bit floating point                  |
| `complex64`  | 32-bit floating point real & imaginary |
| `complex128` | 64-bit floating point real & imaginary |

## Resources / References

- [Go - fmt package](https://pkg.go.dev/fmt)
- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
