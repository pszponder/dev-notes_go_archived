# Strings / Runes in Go

## Intro to Strings / Runes in Go

Strings and Runes are a [basic type](go_data-types_basic.md) in Go

| Data Type | Description            |
| --------- | ---------------------- |
| `string`  | sequence of characters |
| `rune`    | alias for `int32`      |

## Zero Values for Strings / Runes

> Zero value for string is `""`

> Zero value for rune is `0`

## Strings

A `string` is a data type for storing multiple runes

- Defined by wrapping text in double quotes (`""`)
- Simply an [`array`](go_data-types_arrays.md) of `bytes`

## Runes

Text is represented using the `rune` type

- A `rune` is similar to `char` in other languages
- The `rune` type is an alias for `int32`
- A `rune` can represent any symbol (letters, numbers, emoji, etc.)

## Defining Strings / Runes

- Wrap a character in single-quotes (`''`) to define a `rune`
- Wrap a character(s) in double-quotes (`""`) to define a `string`
- Wrap a character(s) in backticks to define a `raw literal`

## Formatting Strings using fmt package

The `fmt` package enables you to format strings and print them to the terminal, data stream, or to a new string

Printing to terminal:

- `Printf` -- custom format
- `Print` -- simple print
- `Println` -- simple print w/ new line

Printing to a Data Stream (instead of terminal):

- `Fprintf` -- custom format
- `Fprint` -- simple print
- `Fprintln` -- simple print w/ new line

Printing to a New String (instead to terminal):

- `Sprintf` -- custom format
- `Sprint` -- simple print
- `Sprintln` -- simple print w/ new line

Commonly used verbs that `Printf` / `Fprintf` / `Sprintf` in Go can accept and what they represent:

| Verb         | Description                                               |
| ------------ | --------------------------------------------------------- |
| `%s`         | String                                                    |
| `%d` or `%v` | Integer (decimal)                                         |
| `%f`         | Floating-point number (decimal point)                     |
| `%t`         | Boolean (`true` or `false`)                               |
| `%c`         | Character (rune)                                          |
| `%x` or `%X` | Hexadecimal representation (lowercase or uppercase)       |
| `%b`         | Binary representation                                     |
| `%o`         | Octal representation                                      |
| `%p`         | Pointer (address in hexadecimal)                          |
| `%T`         | Type of the value                                         |
| `%e` or `%E` | Scientific notation (lowercase or uppercase)              |
| `%g` or `%G` | Compact notation (either `%e` or `%f` based on the value) |
| `%U`         | Unicode code point (in U+1234 format)                     |

```go
firstName := "Bilbo"
lastName := "Baggins"
age := 100

// Examples of Printf (these are printed to the console)
fmt.Printf("Hello World!\n")
fmt.Printf("Name: %s %s, Age: %d\n", firstName, lastName, age)

// Examples of Sprintf (used to create new strings)
formattedString := fmt.Sprintf("Name: %s %s, Age: %d\n", firstName, lastName, age)
```

```go
// Example of Fprintf
package main

import (
    "fmt"
    "os"
)

func main() {
    name := "Charlie"
    age := 35

    // Print formatted output to a specified writer (in this case, to a file)
    file, err := os.Create("output.txt")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer file.Close()

    _, err = fmt.Fprintf(file, "Name: %s, Age: %d\n", name, age)
    if err != nil {
        fmt.Println("Error:", err)
        return
    }

    fmt.Println("Data written to 'output.txt'")
}
```


## Resources / References

- [Go - fmt package](https://pkg.go.dev/fmt)
- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
