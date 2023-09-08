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

## Creating Errors

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

To define a custom error type, make sure to implement the `Error() string` method defined by the built-in `error` [interface](go_data-types_interfaces.md)

```go
// Define custom error type
type MyError struct {
	Message string
}

// Define the `Error()` method to custom error type
func (e *MyError) Error() string {
	return e.Message
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

- Use `recover()` in combination with the `defer` statement to capture and handle panics gracefully

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
- [Go - Error Handling and Go](https://go.dev/blog/error-handling-and-go)
- [NerdCademy - Golang Error Handling | Go Tutorial for Beginners](https://www.youtube.com/watch?v=VMveb4GqRck)
- [Anthony GG - Golang Error Handling is Better Than You Think!](https://www.youtube.com/watch?v=XCXHzfJZ6CA)
