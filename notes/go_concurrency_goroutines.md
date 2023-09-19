# Goroutines

## What are Goroutines

`Goroutines` are lightweight [`threads`](go_concurrency.md#program-vs-process-vs-thread) of execution managed by the Go runtime.

- Enable functions to run [`concurrently`](go_concurrency.md)
- Go will automatically select `parallel` or `asynchronous` execution for a coroutine

> **CAUTION:** The function that starts a goroutine will **not** wait for the goroutine to finish.

- If the enclosing function (parent thread) completes before the spun-off goroutine (child thread), then the spun-off goroutine will e terminated without any warning

### The main Goroutine

**IMPORTANT:** Every Go program has at least one goroutine: the `main goroutine` which runs the `main()` function

- Additional goroutines are started using the `go` keyword
- Each of these additional goroutines are separate threads which are spun up

## How to create a Goroutine

Prefix any function invocation with the `go` keyword to turn it into a goroutine

- Use the `go` keyword to turn any [`anonymous function` / `closure`](go_functions.md#anonymous-functions) invocations into a `goroutine` as well

```go
// This function invocation is a goroutine
// it is executed concurrently
go funcName(args...)
```

> **CAUTION:** If the `main` function finishes, all running `goroutines` will be terminated without any warning.

```go
/* =============== */
/* Basic Goroutine */
/* =============== */

package main

import (
	"fmt"
	"time"
)

func say(s string, n int) {
	for i := 0; i < n; i++ {
		// Pause function execution for 100 milliseconds
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

// This function is actually executed by the main thread / goroutine
func main() {
	// Execute this function in its own thread
	// i.e. Spin off the execution of this function
	// into its own thread
	go say("world", 5)

	fmt.Println("Program started...");

	// Wait for some time to allow the separate thread
	// to complete before moving to the last line and completing
	// and exiting the main thread
	time.Sleep(1000 * time.Millsecond)

	// Once the last line of the main thread completes,
	// the main thread will exit.
	// Any other goroutines running are terminated
	fmt.Println("Program finished.")
}
```

```go
/* =================================== */
/* Goroutines with Anonymous Functions */
/* =================================== */

package main

import (
	"fmt"
	"time"
)

go main() {
	counter := 0

	// Declare an anonymous function
	wait := func(ms time.Duration) {
		time.Sleep(ms * time.Millisecond)
		counter += 1
	}

	fmt.Println("Starting goroutines")
	go wait(100)
	go wait(900)
	go wait(1000)

	fmt.Println("Launched.      Counter=", counter)

	// Make sure wait time >= than total execution time
	// of goroutines
	// Otherwise, main thread will exit
	// and any still running goroutines will be terminated
	time.Sleep(1100 * time.Millesecond)

	fmt.Println("Waited 1100ms. Counter=", counter)
}
```

## Blocking Goroutines with WaitGroups

## Goroutine Communication w/ Channels

[Channels](go_concurrency_channels.md) are used to pass information to and from goroutines

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Kantan Coding - Master Go Programming w/ these concurrency Patterns](https://youtu.be/qyM8Pi1KiiM?feature=shared)
- [NerdCademy - Goroutines and Channels](https://youtu.be/c6DH-1nffTI?feature=shared)
- [Go by Example: Goroutines](https://gobyexample.com/goroutines)
