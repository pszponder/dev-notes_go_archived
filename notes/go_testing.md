# Testing in Go

## Go Testing Intro

In Go, there really isn't much of a distinction between `Unit Testing` and `Integration Testing`

- `Unit Testing` tests individual functions
- `Integration Testing` tests the interactions between functions and modules

## Testing Prerequisites

Before you can run your go tests, need to initialize your project with a [`go.mod`](go_modules.md#initializing-a-go-module) file for your project.

## Naming Conventions

Test files must end in `_test.go`

- Ex. Testing a package named `mypackage.go` requires a test file named `mypackage_test.go`

Test functions within a test file should always be prepended with the word `Test`

- Optionally followed by the name of he function under test

## Creating Test Files in Go

Use the `testing` package to create tests (a test file will always import the `testing` package)

> **NOTE:** A test file must belong to the same package for which its tests are written for

## Writing Test Functions

Test functions have the following signature:

```go
func Test<FuncUnderTestName>(t *testing.T) {
	// ARRANGE -- Define testing environments & values
	// ACT -- Run the code that is being tested
	// ASSERT -- Evaluate result and compare to expected value
}
```

- `t` is the testing handle that provides various testing-related function
- Inside the function:
  - Use `t.Error` or `t.Errorf` to report test failures
  - Use `t.Log` or `t.Logf` for logging information

## Testing Functions from Testing Library

```go
package mypackage

import "testing"

func TestMyFunc(t *testing.T) {
	t.Log()     // similar to fmt.Println() and concurrently safe
	t.Logf()    // similar to fmt.Printf() and concurrently safe

	t.Fail()    // mark test as failed, continue running tests
	t.FailNow() // t.Fail() + safely exist without continuing

	t.Error()   // t.Log() + t.Fail()
	t.Errorf()  // t.Logf() + t.Fail()

	t.Fatal()   // t.Log() + t.FailNow()

	t.run(<desc>, <TestFunc>)  // Run a sub-test
	t.skip(<msg>) // Skip anything below t.skip() of current test
	t.Cleanup(<callbackFunc>) // Invoke callback func before parent function completes
	t.Parallel() // specify a test as a parallel test
}
```

## Running Tests

Use `go test...` cli command to execute tests

```bash
# Get help docs
go help test

# Run all tests in current directory & all subdirectories
go test ./...

# Run tests in a specific directory
go test <path/to/directory>

# Run a specific test file
go run <path/to/filename>_test.go

# Run a specific test function
go test -run <test-func-name>
```

Options:

- `-v` -- run test(s) in verbose mode for extra info
- `-count=1` -- run test(s) without cache
- `--cover` -- return code coverage

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
package mypackage

import (
	"testing"
)

func TestAdd(t *testing.T) {
	// ARRANGE -- Define testing environments & values
	var (
		a = 2
		b = 3
		expected = a + b
	)

	// ACT -- Run the code that is being tested
	result := Add(a, b)

	// ASSERT -- Evaluate result and compare to expected value
	if result != 5 {
		t.Errorf("Expected %d, got %d", expected, result)
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
package mymath

import (
	"testing"
)

func TestSquare(t *testing.T) {
	// ARRANGE -- Define testing environments & values

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
		// ACT -- Run the code that is being tested
		result := Square(test.input)

		// ASSERT -- Evaluate result and compare to expected value
		if result != test.expected {
			t.Errorf("For input %d, expected %d, but got %d", test.input, test.expected, result)
		}
	}
}
```

Example 2:

```go
// math.go
package math

func add(x, y int) int {
	return x + y
}

func subtract(x, y int) int {
	return x - y
}

func multiply(x, y int) int {
	return x * y
}

func divide(x, y float64) float64 {
	if y == 0 {
		panic("cannot divide by zero")
	}

	return x / y
}

```

```go
// math_test.go
package math

import "testing"

func TestAdd(t *testing.T) {
	// ARRANGE -- Define testing environments & values
	var (
		a        = 1
		b        = 2
		expected = a + b
	)

	// ACT -- Run the code that is being tested
	result := add(a, b)

	// ASSERT -- Evaluate result and compare to expected value
	if result != expected {
		t.Errorf("add(%d, %d) FAILED. Expected %d, got %d\n", a, b, expected, result)
	} else {
		t.Logf("add(%d, %d) PASSED. Expected %d, got %d\n", a, b, expected, result)
	}
}

func TestSubract(t *testing.T) {
	// ARRANGE -- Define testing environments & values

	// Define a test table with a list input values and expected results
	// This allows us to run multiple test cases
	tests := []struct {
		a, b     int
		expected int
	}{
		{1, 2, -1},
		{10, 5, 5},
		{1, 1, 0},
		{2, 1, 1},
		{7, -2, 9},
	}

	// Iterate through the test cases
	for _, test := range tests {
		// ACT -- Run the code that is being tested
		result := subtract(test.a, test.b)

		// ASSERT -- Evaluate result and compare to expected value
		if result != test.expected {
			t.Errorf("subtract(%d, %d) FAILED. Expected %d, got %d\n", test.a, test.b, test.expected, result)
		} else {
			t.Logf("subract(%d, %d) PASSED. Expected %d, got %d\n", test.a, test.b, test.expected, result)
		}
	}
}

func TestMultiply(t *testing.T) {
	// ARRANGE -- Define testing environments & values
	var (
		a        = 1
		b        = 2
		expected = a * b
	)

	// ACT -- Run the code that is being tested
	result := multiply(a, b)

	// ASSERT -- Evaluate result and compare to expected value
	if result != expected {
		t.Errorf("multiply(%d, %d) FAILED. Expected %d, got %d\n", a, b, expected, result)
	} else {
		t.Logf("multiply(%d, %d) PASSED. Expected %d, got %d\n", a, b, expected, result)
	}
}

func TestDivide(t *testing.T) {
	// ARRANGE -- Define testing environments & values
	var (
		a        = float64(1)
		b        = float64(2)
		expected = float64(a / b)
	)

	// ACT -- Run the code that is being tested
	result := divide(a, b)

	// ASSERT -- Evaluate result and compare to expected value
	if result != expected {
		t.Errorf("divide(%f, %f) FAILED. Expected %f, got %f\n", a, b, expected, result)
	} else {
		t.Logf("divide(%f, %f) PASSED. Expected %f, got %f\n", a, b, expected, result)
	}
}

func TestDividePanics(t *testing.T) {
	// This IFFE will fire right before the TestDividePanics function returns
	defer func() {
		// recover from panic
		if r := recover(); r == nil {
			t.Errorf("The code did not panic")
		} else {
			t.Logf("A panic occurred as expected")
		}
	}()

	// This should cause a panic, as the denominator is zero
	divide(1, 0)
}

```

## Writing Sub-tests

Use `t.Run()` method to run sub-tests within your tests.

- This is similar how you would nest multiple `it` tests in Vitest / Jest under a single `describe` method in JavaScript

```go
package mymath

import "testing"

// Function to Test
func Square(x int) int {
	return x * x
}

// Test function
func TestSquareSub(t *testing.T) {
	// Run a sub-test
	t.Run("Case 1", func(t *testing.T) {
		// Test logic to run for Case 1
	})

	// Run another sub-test
	t.Run("Case 2", func(t *testing.T) {
		// Test logic to run for Case 2
	})

	// Run a third sub-test
	t.Run("Case 3", func(t *testing.T) {
		// Test logic to run for Case 3
	})
}
```

## Skipping Tests

Use `t.Skip()` or `t.Skipf()` to conditionally skip the remainder of the current test

```go
package main

import (
	"testing"
)

func TestExample(t *testing.T) {
	skipTest := true

	if skipTest {
		t.Skip("Skipping the test as skipTest is true")
		// If t.Skip() is triggered, anything below here won't run
	}

	// If t.Skip() is triggered, anything below here won't run

	// The following line will not be executed if t.Skip() is called
	t.Fatal("This test should have been skipped!")
}
```

## Test Cleanup / Teardown

Use the `t.Cleanup(<callback>)` function to trigger execution of a callback function right before the parent function completes.

- This is the testing equivalent to using the [`defer`](go_functions.md#deferring-function-execution-w-defer-keyword) keyword
- `t.Cleanup(<callback>)` is concurrently safe
- The callback function is invoked regardless if the test passes or fails
- Typically used for closing files, shutting down servers, or disposing of any other resources that need to be cleaned up

```go
package main

import (
	"fmt"
	"testing"
)

func setup() {
	fmt.Println("Setting up test resources")
}

func teardown() {
	fmt.Println("Tearing down test resources")
}

func TestExample(t *testing.T) {
	setup()

	// Schedule the teardown function to be called once
	// the test and all its subtests are done.
	t.Cleanup(teardown)

	// Your test code here
	t.Run("Subtest1", func(t *testing.T) {
		fmt.Println("Running Subtest1")
		// ...
	})

	t.Run("Subtest2", func(t *testing.T) {
		fmt.Println("Running Subtest2")
		// ...
	})

	//...

	// After all tests and sub-tests complete,
	// the callback function passed into t.Cleanup()
	// is invoked
}

/*
Setting up test resources
Running Subtest1
Running Subtest2
Tearing down test resources
*/
```

## Run Tests in Parallel

When you invoke `t.Parallel()` within a test, that test is marked as parallel. This test will run in parallel with other parallel tests.

**NOTE:** When using `t.Parallel()`, you should avoid using global state or anything else that can't be used concurrently to avoid race conditions.

- If you need to use shared state, use synchronization techniques such as mutexes to ensure that your tests remain thread-safe.

In the example below:

- TestOne, TestTwo, and TestThree will be run concurrently

```go
package main

import (
	"fmt"
	"testing"
	"time"
)

// Test function 1
func TestOne(t *testing.T) {
	t.Parallel() // Marks the test as a parallel test

	fmt.Println("Starting TestOne")
	time.Sleep(2 * time.Second) // Simulating some test execution time
	fmt.Println("Ending TestOne")
}

// Test function 2
func TestTwo(t *testing.T) {
	t.Parallel() // Marks the test as a parallel test

	fmt.Println("Starting TestTwo")
	time.Sleep(2 * time.Second) // Simulating some test execution time
	fmt.Println("Ending TestTwo")
}

// Test function 3
func TestThree(t *testing.T) {
	t.Parallel() // Marks the test as a parallel test

	fmt.Println("Starting TestThree")
	time.Sleep(2 * time.Second) // Simulating some test execution time
	fmt.Println("Ending TestThree")
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Go Testing Package](https://pkg.go.dev/testing)
- [Go - Add a Test](https://go.dev/doc/tutorial/add-a-test)
- [NerdCademy - GoLang Unit Testing and Mock Testing Tutorial](https://www.youtube.com/watch?v=XQzTUa9LPU8)
- [Golang Dojo - Golang Testing](https://www.youtube.com/watch?v=FjkSJ1iXKpg)
