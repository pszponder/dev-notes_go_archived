# Concurrency in Go

## Concurrency vs Parallelism in Go

`Concurrency` (Asynchronous in Go):

- Multiple tasks are concurrent if they are in progress at once
  - At any single point of time, one or more of these tasks may be in a running or in a paused state
- Concurrent tasks start, do their work, and end in overlapping periods of time
- In Go, `Asynchronous` code can pause and resume its execution. While a piece of async code is paused, other async code can resume and execute.

`Parallelism` (Threaded in Go):

- Multiple tasks are parallel if they are executing at once
  - At any single point of time, two or more of these tasks are running at the same time
- Usually achieved by utilizing multiple processors / cores
- In Go, `Threaded` code runs in parallel based on the number of CPU cores
  - **NOTE:** You can further control the parallelism of a Go program by setting `runtime.GOMAXPROCS(<#>)` which will limit the number of cores used by the Go program.

> Go will automatically choose the appropriate concurrency method (`Asynchronous` or `Threaded`)

NOTE: `Parallelism` is a subset of `concurrency`

- All parallel running tasks are also running concurrently
- Not all concurrently running tasks are running in parallel
- [Rob Pike - Concurrency is not Parallelism](https://www.youtube.com/watch?v=oV9rvDllKEg)

### Controlling Parallelism in Go

You can define the max number of cores a Go program can use for parallel computations via the `runtime.GOMAXPROCS(<#>)` function.

- **NOTE:** It is ultimately up to the Go scheduler to decide whether to run a goroutine in `parallel` or `concurrently`.

```go
package main

import (
	"fmt"
	"runtime"
	"sync"
)

func main() {
	// Set the maximum number of concurrent threads to 4.
	// This means that up to 4 goroutines can potentially run in parallel
	// on a multi-core machine.
	runtime.GOMAXPROCS(4)

	// Declare a WaitGroup to wait for all goroutines to complete
	//  their execution before exiting.
	// Without this, the program may terminate before all of the
	//  goroutines have a chance to complete their execution.
	var wg sync.WaitGroup

	// Start a loop to launch 4 goroutines.
	for i := 0; i < 4; i++ {
		// Increment the WaitGroup counter.
		wg.Add(1)

		// Start a new goroutine.
		go func(id int) {
			// Decrement the WaitGroup counter when the goroutine completes.
			// This allows the `wg.Wait()` at the end of the main function
			//  to unblock only after all goroutines have completed.
			defer wg.Done()

			// Print the ID of the goroutine.
			fmt.Printf("Goroutine %d is running\n", id)
		}(i) // Pass the value of i to the goroutine as an argument.

		// At this point, the Go runtime will schedule the goroutine to run,
		// potentially in parallel with other goroutines (up to 4 at a time,
		// since GOMAXPROCS is set to 4).
	}

	// Wait for all goroutines to complete their execution before exiting the program.
	wg.Wait()
}
```

## Deterministic vs non-Deterministic Code

`Deterministic` Code:

- Always produces the same output for the same input, irrespective of how many times it is run

`Non-Deterministic` Code:

- Produces different outputs even if you provide the same input on multiple runs
- Concurrent execution can make your code non-deterministic
- Techniques such as `synchronization` is required to ensure that the program works as designed

## Program vs Process vs Thread

A `program` is an executable file stored on the disk

- Contains a set of processing instructions stored

A `process` is a `program` which is loaded onto memory and executed by the processor

- An active process includes resources the program needs to run (CPU, RAM, I/O)
- A process contains
  - Runtime code
  - Data
  - Heap
  - Stack
- Each process has its own memory address space (process isolation)
- Each `process` contains at least one `thread` (`main tread`)

A `thread` is the unit of execution within a `process`

- Each `process` contains at least one `thread` (`main thread`)
- Each `thread` has its own stack
  - Stack
  - Registers
  - Program counters
- `threads` share the same memory address space

## How does Go Handle Concurrency?

### Goroutines (Concurrent Processes)

[`Goroutines`](go_concurrency_goroutines.md)

### Channels (Communication between Goroutines)

[`Channels`](go_concurrency_channels.md)

### WaitGroups (Synchronizing Goroutine Execution)

[`WaitGroups`](go_concurrency_waitgroups.md)

### Mutexes (Concurrent State Management)

[`Mutexes`](go_concurrency_mutexes.md)

## Goroutines vs Coroutines

`Goroutines` and `coroutines` are related concepts as both refer to lightweight threads, but they are not the same and have different characteristics and behaviors. Here’s a breakdown of the differences:

### Goroutines (specific to Go language):

1. **Managed by Go Runtime:**

   - [Goroutines](go_concurrency_goroutines.md) are managed by the Go runtime scheduler. The scheduler multiplexes goroutines onto OS threads in an efficient manner, enabling seamless concurrency.

2. **Concurrency Model:**

   - Goroutines are part of Go’s concurrency model that allows multiple tasks to be executed potentially simultaneously (concurrently or in parallel).

3. **Non-Blocking:**

   - Goroutines often use [channels](go_concurrency_channels.md) for communication which prevent the issues that can arise from sharing memory such as race conditions.

4. **Lightweight:**

   - Goroutines are lighter weight compared to OS threads, allowing you to spawn thousands or even millions of them without exhausting system resources.

5. **Simple Syntax:**
   - Starting a goroutine is as simple as putting the `go` keyword before a function call: `go func()`.

### Coroutines:

1. **General Concept:**

   - Coroutines are a general programming concept available in multiple languages (like Python, Lua, and Kotlin), not tied to a specific language runtime like goroutines.

2. **Cooperative Multitasking:**

   - Coroutines rely on cooperative multitasking, where they yield control back to the scheduler, often explicitly, allowing other coroutines to run.

3. **Blocking and Non-Blocking:**

   - Depending on the language and implementation, coroutines might block or explicitly yield back to the scheduler.

4. **Flexible Scheduling:**
   - Coroutines often allow for more flexible scheduling and coordination, with explicit commands for starting, stopping, and yielding control.

### Goroutines vs Coroutines Summary:

- **Goroutines** are a specific feature of Go, providing a simple and efficient concurrency model, abstracting much of the complexity of thread management and synchronization.

- **Coroutines** are a broader concept, available in many languages, providing cooperative multitasking and flexible scheduling controls.

Both offer ways to write concurrent code, but the mechanisms for scheduling, coordination, and communication can differ substantially.

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Sy Brand - Concurrency vs Parallelism Explained with my cats](https://www.youtube.com/watch?v=5-u0yLci8bU)
- [Jacob Sorber - Is it concurrent or parallel?](https://www.youtube.com/watch?v=r2__Rw8vu1M)
- [ByteByteGo - Process vs Thread](https://www.youtube.com/watch?v=4rLW7zg21gI)
- [Concurrency is not Parallelism by Rob Pike](https://www.youtube.com/watch?v=oV9rvDllKEg)
- [Golang Dojo - Golang Concurrency Basics](https://www.youtube.com/playlist?list=PLve39GJ2D71wSwRQLp_h8B60pKgS85StC)
- [Anthony GG - Learn Golang Concurrency For Beginners By Example](https://www.youtube.com/watch?v=P4tckkcyef0)
