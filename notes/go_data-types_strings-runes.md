# Strings / Runes in Go

## Intro to Strings / Runes in Go

Strings are a [basic](go_data-types_basic.md#strings--runes) [value](go_data-types_value-vs-reference.md#value-types) type in Go

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

## Working with Strings

Can perform various string manipulation operations using the built-in `strings` package, which provides a wide range of functions for working with strings.

### Finding String Length

Use the `len()` function to find the length of a string

```go
str := "Hi!"
length := len(str) // 3
```

### Concatenating Strings

To concatenate strings, can use the `+` operator or the `strings.Join()` function

```go
str1 := "Hello, "
str2 := "world!"

// Using the + operator
concat1 := str1 + str2

// Using strings.Join()
strs := []string{str1, str2}
concat2 := strings.Join(strs, "")
```

> Note: You can also use [`string.Builder`](#string-builder) to build / concatenate strings more efficiently

### Splitting Strings

To split a string into substrings based on a delimiter, you can use `strings.Split()`

```go
str := "apple,banana,cherry"
parts := strings.Split(str, ",") // Splits into []string{"apple", "banana", "cherry"}
```

### Extracting Substrings

To extract a substring from a string, use string slicing

```go
subString := myStr[<startIdx>:<endIdx>]
```

```go
str := "Hello, world!"
sub := str[0:5] // Extracts Hello
```

### Checking If String Contains Substring

To check if a string contains a substring, can use `strings.Contains()`

```go
str := "Hello, world!"
contains := strings.Contains(str, "world") // Returns true
```

### Replacing Substrings

To replace occurrences of a substring in a string, you can use `strings.Replace()`

```go
str := "Hello, world!"
newStr := strings.Replace(str, "world", "Go", -1) // Replaces "world" with "Go"
```

### Trimming Whitespace

To remove leading and trailing whitespace from a string, you can use `strings.TrimSpace()`.

```go
str := "   Hello, world!   "
trimmed := strings.TrimSpace(str) // Removes leading/trailing spaces
```

### Changing Case

To convert a string to uppercase or lowercase, you can use `strings.ToUpper()` and `strings.ToLower()`.

```go
str := "Hello, world!"
upper := strings.ToUpper(str) // Converts to uppercase
lower := strings.ToLower(str) // Converts to lowercase
```

### String Comparison

To compare strings, you can use `==` for equality checks. For case-insensitive comparisons, you can use `strings.EqualFold()`.

```go
str1 := "hello"
str2 := "Hello"
equal := (str1 == str2)                  // Case-sensitive comparison
equalIgnoreCase := strings.EqualFold(str1, str2) // Case-insensitive comparison
```

## String Builder

`strings.Builder` can be used to efficiently build and concatenate strings

- More efficient alternative to repeatedly using string concatenation with the `+` operator because it minimizes memory allocation and copying

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// Create a new strings.Builder
	var builder strings.Builder

	// Append strings to the builder
	builder.WriteString("Hello, ")
	builder.WriteString("world!")

	// Get the final string from the builder
	result := builder.String()

	// Print the result
	fmt.Println(result) // Outputs: Hello, world!
}
```

```go
// Function to join and reverse a sequence of strings
func joinedAndReverse(strs ...string) (string, string) {
	// Create new string builder
	var sb strings.Builder

	// Loop through slice of strings,
	// adding each string to the string builder
	for _, str := range strs {
		sb.WriteString(str)
	}

	// Save the newly built string to a variable
	joined := sb.String()

	// Reset the string builder to reuse it
	sb.Reset()

	// Loop through slice of string backwards
	// write each string to the string builder
	for i := len(strs) - 1; i >= 0; i-- {
		sb.WriteString(strs[i])
	}

	// Save newly reversed string to a variabled
	reversed := sb.String()

	return joined, reversed
}
```

```go
/*
This example shows how to use a string builder
to build a string

`fmt.Fprintf` is used to format a string and write
to an `io.Writer` which is the string builder
in this case.

Ths is possible because string builder
implements the `io.Writer` interface.
*/

package main

import (
	"fmt"
	"strings"
)

func main() {
	// Create a new strings.Builder
	var sb strings.Builder

	// Loop from 3 down to 1 (inclusive)
	for i := 3; i >= 1; i-- {
		// Use fmt.Fprintf to format and append
		// the current value of 'i'
		// followed by "..." to the 'sb' Builder
		fmt.Fprintf(&sb, "%d...", i)
	}

	// Append the string "ignition" to the 'sb' Builder
	sb.WriteString("ignition")

	// Print the final string constructed in the Builder
	// using sb.String()
	fmt.Println(sb.String()) // 3...2...1...ignition
}

```

Check out this link for more info: [Jon Calhoun - Concatenating and Building Strings in Go 1.10+](https://www.calhoun.io/concatenating-and-building-strings-in-go/)

## Resources / References

- [Go - strings package](https://pkg.go.dev/strings)
- [Go - strings builder](https://pkg.go.dev/strings#Builder)
- [Go - fmt package](https://pkg.go.dev/fmt)
- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Jon Calhoun - Concatenating and Building Strings in Go 1.10+](https://www.calhoun.io/concatenating-and-building-strings-in-go/)
- [Jon Calhoun - 6 Tips for Using Strings in Go](https://www.calhoun.io/6-tips-for-using-strings-in-go/)
