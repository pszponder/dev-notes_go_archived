# Blocking Goroutines with WaitGroups

## Intro to WaitGroups

`WaitGroups` are synchronization primitives from the `sync` package used for waiting for a collection of [goroutines](go_concurrency_goroutines.md) to finish executing.

- Useful in concurrent programs, where you need to ensure that all goroutines complete their execution before proceeding.
- `WaitGroup` allows you to easily coordinate and wait for the completion of multiple goroutines in a concurrent Go program

## Wait Group Methods:

1. `Add(#)`
   1. Increments `WaitGroup` counter by `#` (`int` type)
   2. When you launch a goroutine, call `Add(1)` to increment the `WaitGroup` counter by 1
2. `Done()`
   1. Decrements `WaitGroup` counter by 1
   2. Equivalent to invoking `Add(-1)`
   3. Usually defer this method call at the beginning of the goroutine to ensure that it's called when the goroutine exits
3. `Wait()`
   1. Blocks the calling goroutine until the `WaitGroup` counter is decremented to `0`
   2. Typically used in the `main` function (or the spawning goroutine) to block until all spawned goroutines have called `Done()`, indicating they have finished execution

```go
package main

import (
	"fmt"
	"sync"
)

func worker(id int, wg *sync.WaitGroup) {
	defer wg.Done() // Decrement the counter when the goroutine completes.

	fmt.Printf("Worker %d starting\n", id)
	// PERFORM WORK HERE...
	fmt.Printf("Worker %d done\n", id)
}

func main() {
	var wg sync.WaitGroup // Declare a WaitGroup.

	// Start several worker goroutines, incrementing the WaitGroup counter for each.
	for i := 1; i <= 5; i++ {
		wg.Add(1) // Increment the WaitGroup counter by 1
		go worker(i, &wg) // Spin off a goroutine
	}

	// Wait for all worker goroutines to finish.
	wg.Wait() // BLOCK until all worker goroutines have completed

	fmt.Println("All workers completed.")
}
```

## Resources / References
