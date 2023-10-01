# Concurrent State Management with Mutexes

## Intro to Mutexes

`Mutex`es are used for managing state concurrently.

- A `mutex` ensures that only one [goroutine](go_concurrency_goroutines.md) has access to a particular piece of state at a time.
- Crucial when multiple goroutines are accessing and modifying shared data, as it prevents race conditions and ensures data consistency.
- **NOTE:** Mutexes are not necessarily tied to a piece of state, instead, they are used to ensure that a section of code that accesses the piece of state is executed by only one goroutine at a time.

## Mutex Best Practices

- A mutex variable should be prefixed with `mu`

## How to use a Mutex

1. Import the `sync` package
2. Create an instance of a `sync.Mutex`
3. Use the `Lock()` method to lock the mutex before accessing a shared resource
4. Use the `Unlock()` method to unlock the mutex after accessing the shared resource
   1. Can use the [`defer`](go_functions.md#deferring-function-execution-w-defer-keyword) keyword to invoke the unlock function right before the parent function completes.

Example of using a mutex to manage state concurrent incrementing of a counter state variable.

```go
package main

import (
	"fmt"
	"sync"
)

// Define a state variable (counter) and a mutex
var (
	counter int
	mutex sync.Mutex
)

// incrementCounter increments the counter variable.
func incrementCounter(wg *sync.WaitGroup) {
	// Locking the mutex to ensure exclusive access
	// to the counter variable.
	mutex.Lock()

	counter++ // Increment the counter

	// Unlocking the mutex to allow other
	// goroutines to access the counter.
	mutex.Unlock()

	// Indicating that this goroutine is done.
	wg.Done()
}

func main() {
	// Creating a wait group to wait for all goroutines to finish.
	var wg sync.WaitGroup

	// Starting 100 goroutines to increment the counter.
	for i := 0; i < 100; i++ {
		wg.Add(1) // Incrementing the wait group counter.
		go incrementCounter(&wg) // Starting the goroutine.
	}

	// Waiting for all goroutines to finish.
	wg.Wait()

	// Printing the final value of the counter.
	fmt.Println(counter)
}
```

This example demonstrates how to safely append to a slice from multiple goroutines concurrently:

```go
/**
- The addNumber function is used to add a number to the numbers slice.
- Before appending to the slice, it locks the mutex to ensure
  exclusive access to the slice.
- After appending the number, it unlocks the mutex,
  allowing other goroutines to add numbers to the slice.
- The main function launches 10 goroutines to add numbers from 0 to 9 to the slice,
  and then it waits for all goroutines to finish before printing the numbers slice.
*/

package main

import (
	"fmt"
	"sync"
)

var (
	numbers []int // Shared state

	// Mutex to protect access to the slice
	mutex sync.Mutex
)

// Adds a number to the numbers slice (shared state)
func addNumber(n int, wg *sync.WaitGroup) {
	mutex.Lock() // Lock the mutex, preventing
	numbers = append(numbers, n)
	mutex.Unlock()
	wg.Done()
}

func main() {
	var wg sync.WaitGroup // Declare the WaitGroup

	// Spin off a new goroutine on each iteration
	for i := 0; i < 10; i++ {
		wg.Add(1) // Increment WaitGroup counter
		go addNumber(i, &wg)
	}

	wg.Wait() // Block code until all goroutines complete

	fmt.Println(numbers)
}
```

The example below uses multiple goroutines to write to and read from a shared map

```go
/**
- The incrementKey function is used to increment the count
  for a specific key in the counts map.
- It locks the mutex before accessing the map and unlocks it afterward.
- The printMap function prints the map,
  and it also locks and unlocks the mutex before and after printing
  the map to ensure that it is not modified while being printed.
- The main function initializes the counts map and starts multiple
  goroutines to increment counts for three keys ("a", "b", "c") in the map.
  It waits for all goroutines to finish before printing the final
  counts for each key in the map.
*/
package main

import (
	"fmt"
	"sync"
)

var (
	// Shared map
	counts map[string]int
	// Mutex to protect access to the map
	mutex sync.Mutex
)

// incrementKey increments the value of a specific key in the map.
func incrementKey(key string, wg *sync.WaitGroup) {
	mutex.Lock()
	counts[key]++
	mutex.Unlock()
	wg.Done()
}

// printMap prints the map.
func printMap() {
	mutex.Lock()
	fmt.Println(counts)
	mutex.Unlock()
}

func main() {
	counts = make(map[string]int)
	var wg sync.WaitGroup

	keys := []string{"a", "b", "c"}

	for _, key := range keys {
		for i := 0; i < 5; i++ {
			wg.Add(1)
			go incrementKey(key, &wg)
		}
	}

	wg.Wait()
	printMap()
}
```

## How does a Mutex Work?

- When `mutex.Lock()` is invoked, the mutes is locked by the calling goroutine.
- Any other goroutines that attempt to lock he same mutex by calling `mutex.Lock()` will block (i.e., they will not proceed further in their execution) until the mutex is unlocked by the holding goroutine with a call to `mutex.Unlock()`

```go
var mu sync.Mutex

func goroutine1() {
	mu.Lock()
	// Critical section: Only one goroutine can execute this section at a time.
	// ...
	mu.Unlock()
}

func goroutine2() {
	mu.Lock()
	// If goroutine1 holds the lock,
	// goroutine2 will block here until the mutex is unlocked.
	// ...
	mu.Unlock()
}
```

In the example above:

- If _goroutine1_ acquires the lock by calling _mu.Lock()_, any subsequent call to _mu.Lock()_ from _goroutine2_ will block until _goroutine1_ calls _mu.Unlock()_.
- Once _mu.Unlock()_ is called by _goroutine1_, _goroutine2_ will unblock and acquire the lock, allowing it to proceed with its execution.
- So effectively, the `mutex.Lock()` and `mutex.Unlock()` calls are used to ensure that the section of code between them is executed by only one goroutine at a time, preventing concurrent access and potential data races.

## Managing Multiple State Variables

You can either use a single `mutex` to manage multiple state variables or use a new `mutex` for each individual piece of state.

### Using a Single Mutex to Manage Multiple State Variables

- PRO: Simpler than using multiple mutexes and avoids the problem of deadlocks
- CON: Even if each goroutine is trying to manipulate a different state variable, the single mutex must be used to facilitate manipulation of all state variables so this creates a bottleneck.

```go
// The "mutex" variable is used to manage both state variables
var (
	counter1 int
	counter2 int
	mutex    sync.Mutex
)

func incrementCounter1() {
	mutex.Lock()
	counter1++
	mutex.Unlock()
}

func incrementCounter2() {
	mutex.Lock()
	counter2++
	mutex.Unlock()
}
```

### Using a New Mutex to Manage Each State Variable

- PRO: Leads to better performance because different goroutines can lock different mutexes at the same time
- CON: Increased complexity and potential for deadlocks if not managed carefully

```go
var (
	counter1 int
	counter2 int
	mutex1   sync.Mutex
	mutex2   sync.Mutex
)

func incrementCounter1() {
	mutex1.Lock()
	counter1++
	mutex1.Unlock()
}

func incrementCounter2() {
	mutex2.Lock()
	counter2++
	mutex2.Unlock()
}
```

Here is an example where using multiple mutexes can lead to a deadlock

```go
/**
The process1 function locks mutex1 first and then mutex2.

The process2 function locks mutex2 first and then mutex1.

This can lead to a situation where each goroutine holds one mutex
and is waiting for the other, causing a deadlock.

Both process1 and process2 are started as goroutines in the main function,
and `select {}` is used to prevent main from exiting,
allowing the goroutines to run indefinitely.
*/

package main

import (
	"fmt"
	"sync"
	"time"
)

var (
	// Define two global mutexes.
	mutex1 sync.Mutex
	mutex2 sync.Mutex
)

// process1 tries to acquire lock on mutex1 and then on mutex2.
func process1() {
	for {
		// Locking the first mutex.
		mutex1.Lock()
		fmt.Println("Process1 acquired lock on mutex1")
		// Simulating some processing by sleeping.
		time.Sleep(1 * time.Second)

		// Trying to lock the second mutex.
		mutex2.Lock()
		fmt.Println("Process1 acquired lock on mutex2")
		time.Sleep(1 * time.Second)

		// Unlocking both mutexes.
		mutex2.Unlock()
		mutex1.Unlock()
	}
}

// process2 tries to acquire lock on mutex2 and then on mutex1.
func process2() {
	for {
		// Locking the second mutex.
		mutex2.Lock()
		fmt.Println("Process2 acquired lock on mutex2")
		// Simulating some processing by sleeping.
		time.Sleep(1 * time.Second)

		// Trying to lock the first mutex.
		mutex1.Lock()
		fmt.Println("Process2 acquired lock on mutex1")
		time.Sleep(1 * time.Second)

		// Unlocking both mutexes.
		mutex1.Unlock()
		mutex2.Unlock()
	}
}

func main() {
	// Starting both processes as goroutines.
	go process1()
	go process2()

	// Preventing the main function from exiting,
	// so that the goroutines continue to run indefinitely.
	select {}
}
```

### Avoiding Deadlocks with Mutexes

- **Always Acquire Locks in the Same Order:**
  - Ensure that all goroutines always acquire the locks in the same order.
- **Use Timeout When Trying to Acquire a Lock:**
  - Use a timeout when trying to acquire a lock so that if it cannot obtain the lock, it can abort instead of waiting indefinitely.
- **Use sync/atomic Package for Simple Mutual Exclusion:**
  - If you only need to protect a single variable, often the sync/atomic package can be used to manage access to the variable without needing to use mutexes.
- **Consider Using Higher-Level Concurrency Control:**
  - Sometimes more complex concurrency control structures or approaches, such as channels or using a single goroutine to manage access to shared resources, can help to avoid deadlocks.

### Choosing Between using Single and Multiple Mutexes

Use a single mutex when:

- You are manipulating multiple state variables at the same time
- Different state variables are coupled / dependent on each other

Use multiple mutexes when:

- Manipulating different state variables which aren't coupled / dependent on one another

## Atomic Values

The `sync/atomic` package in Go provides low-level atomic memory primitives that can be used to manage state in a concurrent program.

- These atomic operations are used for safely changing values of shared variables without using mutex locks.
- The operations include atomic addition, atomic comparison and swap (CAS), and atomic load and store, among others.
- Potentially more efficient and faster than using mutexes
- Use with caution since can be harder to use correctly than mutexes or channels.

### Atomic Add

The `atomic.AddInt64()` function atomically adds two integers and returns the updated value. This can be used to safely increment a counter in a concurrent environment without using a mutex.

Example:

```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

var counter int64

func incrementCounter(wg *sync.WaitGroup) {
	atomic.AddInt64(&counter, 1) // Atomically increment the counter by 1.
	wg.Done()
}

func main() {
	var wg sync.WaitGroup
	for i := 0; i < 1000; i++ {
		wg.Add(1)
		go incrementCounter(&wg)
	}
	wg.Wait()
	fmt.Println("Final Counter:", counter)
}
```

In this example, 1000 goroutines increment the `counter` variable concurrently. The `atomic.AddInt64` function ensures that these increments are atomic, preventing race conditions.

### Atomic Compare and Swap (CAS)

The `atomic.CompareAndSwapInt64()` function atomically compares a variable's current value with a given old value, and if they are equal, it sets the variable to a new value.

Example:

```go
package main

import (
	"fmt"
	"sync/atomic"
)

var value int64

func main() {
	value = 1
	ok := atomic.CompareAndSwapInt64(&value, 1, 2) // Compare value with 1 and if equal, set it to 2.
	fmt.Println(ok, value) // Output: true 2
}
```

In this example, `value` is initially set to `1`. The `atomic.CompareAndSwapInt64` function checks if `value` is `1`, and since it is, it sets `value` to `2` and returns `true`.

### Atomic Load and Store

The `atomic.LoadInt64()` and `atomic.StoreInt64()` functions can be used to safely load and store values in a concurrent program.

Example:

```go
package main

import (
	"fmt"
	"sync/atomic"
)

var config int64

func loadConfig() int64 {
	return atomic.LoadInt64(&config)
}

func updateConfig(newConfig int64) {
	atomic.StoreInt64(&config, newConfig)
}

func main() {
	updateConfig(100)
	fmt.Println(loadConfig()) // Output: 100
}
```

In this example, `config` is a shared variable that is safely loaded using `atomic.LoadInt64` and updated using `atomic.StoreInt64`, ensuring that reads and writes to `config` are atomic.

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Golang Dojo - Golang Concurrency Basics](https://www.youtube.com/playlist?list=PLve39GJ2D71wSwRQLp_h8B60pKgS85StC)
- [Anthony GG - Learn Golang Concurrency For Beginners By Example](https://www.youtube.com/watch?v=P4tckkcyef0)
