# Testing in Go

## Go Testing Intro

In Go, there really isn't much of a distinction between `Unit Testing` and `Integration Testing`

- `Unit Testing` tests individual functions
- `Integration Testing` tests the interactions between functions and modules

## Naming Conventions

Test files should have a file name ending in `_test.go`

- Ex. Testing a package named `mypackage.go` requires a test file named `mypackage_test.go`

Test functions within a test file should always be prepended with the word `Test` followed by the name of the function under test.

## Creating Test Files in Go

Use the `testing` package to create tests (a test file will always import the `testing` package)

> **NOTE:** A test file must belong to the same package for which its tests are written for

## Writing Test Functions

Test functions have the following signature:

```go
func Test<FuncUnderTestName>(t *testing.T) {
    //...
}
```

- `t` is the testing handle that provides various testing-related function
- Inside the function:
  - Use `t.Error` or `t.Errorf` to report test failures
  - Use `t.Log` or `t.Logf` for logging information

## Testing Functions from Testing Library

- `Fail()` -- Mark the test as failed, continue running tests
- `Errorf(<string>)` -- Fail test and add a message, continue running tests
- `FailNow()` -- Mark test as failed & abort current test
- `Fatalf(<string>)` -- Fail and abort test & add a message
- `Logf()` -- Equivalent to `Printf`, but only prints when a test fails

## Running Tests

Use `go test` cli command to execute tests

```bash
go test [-v] <path/to/package>
```

- `-v` is for verbose

## Example of Tests in Go

Package under test:

```go
// mypackage.go
package mypackage

func Add(a, b int) int {
    return a + b
}
```

Test Package:

```go
// mypackage_test.go
package mypackage_test

import (
    "testing"
    "mypackage"
)

func TestAdd(t *testing.T) {
    result := mypackage.Add(2, 3)
    if result != 5 {
        t.Errorf("Expected 5, got %d", result)
    }
}
```

## Table-Driven Tests with Test Tables

`Test tables` are a common practice where you define a table of input values and their expected results, making it easier to add and manage multiple test cases.

```go
// mymath.go
package mymath

func Square(x int) int {
    return x * x
}
```

```go
// mymath_test.go
package mymath_test

import (
    "testing"
    "mymath"
)

func TestSquare(t *testing.T) {
    // Define a test table with input values and expected results
    tests := []struct {
        input    int
        expected int
    }{
        {0, 0},
        {1, 1},
        {2, 4},
        {3, 9},
        {4, 16},
    }

    // Iterate through the test cases
    for _, test := range tests {
        result := mymath.Square(test.input)

        if result != test.expected {
            t.Errorf("For input %d, expected %d, but got %d", test.input, test.expected, result)
        }
    }
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Go Testing Package](https://pkg.go.dev/testing)
