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

> Go will automatically choose the appropriate concurrency method (`Asynchronous` or `Threaded`)

NOTE: `Parallelism` is a subset of `concurrency`

- All parallel running tasks are also running concurrently
- Not all concurrently running tasks are running in parallel
- [Rob Pike - Concurrency is not Parallelism](https://www.youtube.com/watch?v=oV9rvDllKEg)

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

### Goroutines

[`Goroutines`](go_concurrency_goroutines.md)

### Channels

[`Channels`](go_concurrency_channels.md)

### Mutexes

[`Mutexes`](go_concurrency_mutexes.md)

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Sy Brand - Concurrency vs Parallelism Explained with my cats](https://www.youtube.com/watch?v=5-u0yLci8bU)
- [Jacob Sorber - Is it concurrent or parallel?](https://www.youtube.com/watch?v=r2__Rw8vu1M)
- [ByteByteGo - Process vs Thread](https://www.youtube.com/watch?v=4rLW7zg21gI)
- [Concurrency is not Parallelism by Rob Pike](https://www.youtube.com/watch?v=oV9rvDllKEg)
