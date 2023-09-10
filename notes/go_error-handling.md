# Error Handling in Go

## Intro to Error Handling in Go

In Go, error handling is done using a combination of return values and the `error` interface

- Go functions can return an error as the last return value
- If no error occurred, then `nil` is returned
- Errors implement the `error` interface from the `std` library

> **NOTE:** Go does not use `try / catch` blocks for error handling

## Error Interface

The `error` interface is a built-in interface in Go that has a single method: `Error() string`

- Any type that implements this method becomes an `error`

```go
// Built-in error interface in Go looks like this:
type error interface {
  Error() string
}
```

## Creating New Errors

### errors.new()

Use `errors.New()` to create a new error value with a given error message

- Returns an error value that implements the `error` interface
- Can access the error's message using the `Error()` method on the error value

```go
// To generate a new error,
// use the "errors" std library package
err := errors.New(<err-msg>)

// Access the error message from the error value
errMsg := err.Error() // <err-msg>
```

```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	// Create a new error value with a message
	err := errors.New("This is an error message")

	// Access error message using `Error()` method
	fmt.Println(err.Error()) // Output: This is an error message
}
```

### fmt.Errorf()

`fmt.Errorf()` is used to create formatted error messages and return a new error

```go
// Function definition for Errorf function
func Errorf(format string, a ...interface{}) error
```

- `format` is a string that specifies the format of the error message, similar to how it works with `fmt.Sprintf()`
- `a ...interface{}` is a [variadic parameter](go_functions.md#variable-number-of-parameters-w-variadic-functions) that allows you to pass values to be inserted into the format string

```go
package main

import (
	"errors"
	"fmt"
)

func divide(a, b int) (int, error) {
	if b == 0 {
		// Format an error message, pass it into an error, and return it
		return 0, fmt.Errorf("division by zero: %d / %d", a, b)
	}
	return a / b, nil
}

func main() {
	result, err := divide(10, 0)
	if err != nil {
		fmt.Println("Error:", err)
	} else {
		fmt.Println("Result:", result)
	}
}
```

## Returning Errors

Functions that can produce errors typically return a result value along with an error.

Conventions for Returning Errors:

- The error should be the last value returned by the function
- If defined as a variable within the function body, the error is typically named `err`

```go
package main

import (
	"errors"
)

// This function returns an error as the last value
func divide(a, b int) (int, error) {
	if b == 0 {
		return 0, errors.New("division by zero")
	}
	// If no error, return nil as the error value
	return a / b, nil
}
```

## Error Checking

When calling a function that returns an error, always check the error value

- This is typically done using an `if` statement to check whether the received `err` value is not `nil`
- If the error is `nil` it means no error occurred, and you can safely use the result
- If the error is non-`nil`, then there is an error and you need to handle it before proceeding

```go
// Store value and error from function invocation
result, err := divide(10, 2)

// Check error isn't nil before proceeding
if err != nil {
	fmt.Println("Error:", err)
} else {
	fmt.Println("Result:", result)
}
```

## Custom Error Types

To define a custom error type, make sure to implement the `Error() string` method defined by the built-in `error` [interface](go_data-types_interfaces.md) via a pointer receiver function.

> **IMPORTANT:** Make sure your custom error is defined via a pointer receiver function

```go
// Define custom error type
type MyError struct {
	Message string
}

// Define the `Error()` method for custom error type
// NOTE: This MUST be a `pointer receiver` function
func (e *MyError) Error() string {
	return e.Message
}
```

Implementing and using the custom error type:

```go
package main

import "fmt"

// Define custom error type for when dividing by zero
type DivError struct {
	a, b int
}

// Define the `Error()` method for custom error type
// NOTE: This MUST be a `pointer receiver` function
func (d *DivError) Error() string {
	return fmt.Sprintf("Cannot divide by zero: %d / %d", d.a, d.b)
}

// Create a divide function which can return the custom error
func divide(a, b int) (int, error) {
	if b == 0 {
		// Since DivError implements error interface,
		// we can use it here
		return 0, &DivError{a, b}
	} else {
		// If no error, return nil instead of the error
		// As the last function return
		return a / b, nil
	}
}

func main() {
	// Assign variables to value and error return
	// of invoking the "divide" method
	quotient, err := divide(10, 0)

	// Check whether or not there was an error before proceeding
	if err != nil {
		fmt.Println(err)
		// do other stuff to handle error here...
	}

	// If no error, do stuff here...
}
```

### Checking for Specific Errors

Use the `errors.Is()` method (part of the `errors` package) to check if an error or any of its underlying errors are equal to a specified target error.

- Useful to enable to to handle specific errors
- Useful when dealing with wrapped / layered errors

```go
// Function definition of `errors.Is()` method
// `err` is the error to check
// `target` is the error you're searching for
func Is(err, target error) bool
```

```go
package main

import (
	"errors"
	"fmt"
)

func main() {
	// Create some errors
	err1 := errors.New("Error 1")
	err2 := errors.New("Error 2")
	wrappedErr := fmt.Errorf("Wrapped: %w", err2)

	// Check if an error is equal to a target error
	fmt.Println(errors.Is(err1, err2))        // false (err1 is not equal to err2)
	fmt.Println(errors.Is(wrappedErr, err2))  // true (wrappedErr contains err2)
	fmt.Println(errors.Is(wrappedErr, err1))  // false (wrappedErr does not contain err1)
}
```

```go
package main

import (
	"errors"
	"fmt"
)

// Define custom error types
type NotFoundError struct{ Msg string }
type PermissionDeniedError struct{ Reason string }

// Implement the Error() method for custom error types
func (e NotFoundError) Error() string {
	return e.Msg
}

func (e PermissionDeniedError) Error() string {
	return e.Reason
}

func main() {
	// Create instances of custom errors
	notFoundErr := NotFoundError{Msg: "Resource not found"}
	permissionErr := PermissionDeniedError{Reason: "Insufficient permissions"}

	// Simulate an API function that returns an error
	apiError := func() error {
		// Simulate a "not found" error
		return notFoundErr
	}()

	// Check for specific errors using errors.Is
	if errors.Is(apiError, notFoundErr) {
		fmt.Println("API returned a 'not found' error:", apiError)
	} else if errors.Is(apiError, permissionErr) {
		fmt.Println("API returned a 'permission denied' error:", apiError)
	} else {
		fmt.Println("API returned an unknown error:", apiError)
	}
}
```

### Converting Error Types

The `errors.As()` function is used to check if an error value or any of its underlying errors can be type asserted to a specific error type

- It is similar to `errors.Is()`, but it allows you to extract the underlying error of a specific type for further processing.

```go
// Function declaration for `errors.As()`
func As(err error, target interface {}) bool
```

- `err` is the error value to check
- `target` is a pointer to a variable of the error type you want to assert. It should be of type `error`
- Returns `true` if `err` is not `nil` and can be type asserted to `target` and it performs the type assertions, setting `target` to the underlying error value of the correct type
- If the type assertion fails or if `err` is `nil`, `false` is returned

```go
package main

import (
	"errors"
	"fmt"
	"os"
)

func main() {
	// Create a custom error type
	type FileNotFoundError struct {
		Path string
	}

	// Implement the Error() method for the custom error type
	func (e FileNotFoundError) Error() string {
		return fmt.Sprintf("File not found: %s", e.Path)
	}

	// Simulate an error that wraps a custom error
	wrappedErr := fmt.Errorf("Wrapped error: %w", FileNotFoundError{"myfile.txt"})

	// Attempt to extract the custom error
	var fileNotFoundErr FileNotFoundError
	if errors.As(wrappedErr, &fileNotFoundErr) {
		fmt.Println("Custom error type extracted:", fileNotFoundErr.Error())
	} else {
		fmt.Println("Custom error type not found in the wrapped error")
	}

	// Another example: Using errors.As to extract os.PathError
	fileOpenErr := os.ErrNotExist
	var pathErr *os.PathError
	if errors.As(fileOpenErr, &pathErr) {
		fmt.Println("os.PathError extracted:", pathErr.Path)
	} else {
		fmt.Println("os.PathError not found in the error")
	}
}
```

## Panic and Recover

Use `panic()` and `recover()` methods to handle severe errors

### panic

`panic()` is a built-in function used to trigger a runtime panic

- A `panic()` is a program error that causes the program to terminate abruptly
- When `panic()` is invoked, it immediately stops the normal execution flow and starts unwinding the call stack, running any deferred functions (via the `defer` keyword) along the way
  - If no `recover` is used to handle the panic, the program terminates with a stack trace
- Common situations where panics might occur include division by zero, attempting to access an out-of-bounds array index, or dereferencing a `nil` pointer

```go
func main() {
	// Trigger a panic
	panic("Something went terribly wrong!")
}
```

### recover

`recover()` is a built-in function which enables the program to regain control after a panic

- Use `recover()` in combination with the [`defer`](go_functions.md#deferring-function-execution-w-defer-keyword) statement to capture and handle panics gracefully

```go
func main() {
	// create a deferred function to be executed
	// when surrounding function (main in this case) exits
	defer func() {
		// Use recover() to check if panic occurs
		// If panic occurs, recover returns the value
		// passed into panic
		// Otherwise, recover returns nil
		if r := recover(); r != nil {
			fmt.Println("Recovered from panic:", r)
		}
	}()

	// Trigger a panic
	panic("Something went terribly wrong!")
}
```

## Terminating a Program with an Error

You can use the `log.Fatal()` method from the standard library's `log` package to log a message to the standard error stream and terminate the program with a non-zero exit status

```go
package main

import (
	"log"
)

func main() {
	log.Fatal("A fatal error occurred")
	// The program will exit here with a non-zero status code.
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Go - Error Handling and Go](https://go.dev/blog/error-handling-and-go)
- [NerdCademy - Golang Error Handling | Go Tutorial for Beginners](https://www.youtube.com/watch?v=VMveb4GqRck)
- [Anthony GG - Golang Error Handling is Better Than You Think!](https://www.youtube.com/watch?v=XCXHzfJZ6CA)
