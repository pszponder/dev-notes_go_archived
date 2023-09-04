# Basic Types in Go

## Strings / Runes

| Data Type | Description            |
| --------- | ---------------------- |
| `string`  | sequence of characters |
| `rune`    | alias for `int32`      |

> Zero value for string is `""`

> Zero value for rune is `0`

### Strings

A `string` is a data type for storing multiple runes

- Defined by wrapping text in double quotes (`""`)
- Simply an [`array`](go_data-types_arrays.md) of `bytes`

### Runes

Text is represented using the `rune` type

- A `rune` is similar to `char` in other languages
- The `rune` type is an alias for `int32`
- A `rune` can represent any symbol (letters, numbers, emoji, etc.)

### Defining Strings / Runes

- Wrap a character in single-quotes (`''`) to define a `rune`
- Wrap a character(s) in double-quotes (`""`) to define a `string`
- Wrap a character(s) in backticks to define a `raw literal`

## Booleans

| Data Type | Description       |
| --------- | ----------------- |
| `bool`    | `true` or `false` |

> Zero value for a boolean is `false`

## Numbers

Categories of numbers in Go:

- Integers (signed & unsigned)
- Floating-point numbers
- Complex Numbers

> Zero value for integer types is `0`

> Zero value for float types is `0.0`

> Zero value for complex types is `0 + 0i`

### Signed Integer Types

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

### Unsigned Integer Types

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

### Floating Point Types

| Data Type    | Description                            |
| ------------ | -------------------------------------- |
| `float32`    | 32-bit floating point                  |
| `float64`    | 64-bit floating point                  |
| `complex64`  | 32-bit floating point real & imaginary |
| `complex128` | 64-bit floating point real & imaginary |

## Resources / References

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
