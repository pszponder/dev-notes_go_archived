# Context Package

## Intro to the Context Package

The `context` package is used to carry deadlines, cancellations, and other request-scoped values across API boundaries and between processes

- Commonly used in networking (APIs / databases), where one request may involve many different components and take a lot of time, or in concurrent programs to propagate cancellations

## Creating a Context

In Go, creating a new `context` involves using one of the functions provided by the `context` package. Below are the common functions used to create a new context:

### 1. `context.Background()`

This function returns a non-nil, empty Context. It is typically used as the top-level Context for incoming requests or other operations.

```go
ctx := context.Background()
```

### 2. `context.TODO()`

This function returns a non-nil, empty Context as well. Developers are advised to use `context.TODO` when it's unclear which Context to use or the function has not yet been extended to accept a Context parameter.

```go
ctx := context.TODO()
```

### 3. `context.WithCancel(parentContext context.Context)`

This function creates a new context from its parent context that can be cancelled explicitly.

```go
parentCtx := context.Background()
ctx, cancel := context.WithCancel(parentCtx)
defer cancel()
```

### 4. `context.WithTimeout(parentContext context.Context, timeout time.Duration)`

This function creates a new context from its parent with a specified timeout duration.

```go
parentCtx := context.Background()
ctx, cancel := context.WithTimeout(parentCtx, time.Second*5)
defer cancel()
```

### 5. `context.WithDeadline(parentContext context.Context, d time.Time)`

This function creates a new context with a specific deadline time.

```go
parentCtx := context.Background()
d := time.Now().Add(5 * time.Minute)
ctx, cancel := context.WithDeadline(parentCtx, d)
defer cancel()
```

### 6. `context.WithValue(parentContext context.Context, key, val interface{})`

This function returns a new context with the given key-value pair. It should be used to carry request-scoped data.

```go
parentCtx := context.Background()
ctx := context.WithValue(parentCtx, "userID", 1)
```

Here are some examples:

```go
// Creating a new background context
ctx := context.Background()

// Creating a new TODO context
ctx := context.TODO()

// Creating a new cancellable context
ctx, cancel := context.WithCancel(context.Background())
// don't forget to call the cancel function to release resources
defer cancel()

// Creating a new context with a timeout
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

// Creating a new context with a deadline
deadline := time.Now().Add(2 * time.Second)
ctx, cancel := context.WithDeadline(context.Background(), deadline)
defer cancel()

// Creating a new context with a value
ctx := context.WithValue(context.Background(), "key", "value")
```

Each of these context-creating functions returns a new context and a `cancel` function (except `context.Background()` and `context.TODO()`). The `cancel` function is used to release resources and send a cancel signal to the context. It’s a good practice to always call `cancel` using `defer`, unless you have a good reason not to.

## Using Context

### Context with Timeout

- Use `context.WithTimeout(parentContext, timeout)` to create a new context that will automatically be cancelled after the timeout duration
  - **NOTE:** It is still good practice to setup `defer cancel()`

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// create a context that will timeout after 1 second
	ctx, cancel := context.WithTimeout(context.Background(), 1*time.Second)
	defer cancel() // remember to cancel the context to release resources

	// Block until one of two cases is triggered:
	select {
	case <-ctx.Done():
		// Executed if context is done (either cancelled, deadline exceeded, etc.)
		fmt.Println("Context Done:", ctx.Err())
	case <-time.After(3 * time.Second):
		// Executed if no other cases are ready after time duration of 3 seconds
		fmt.Println("No timeout")
	}
}
```

```go
package main

import (
	"context"
	"errors"
	"fmt"
	"log"
	"time"
)

// Depending if this function takes > or <= time than the timeout time defined by ctx,
// This function either succeeds or fails
func callThirdPartyApi(context context.Context, userId int) (bool, error) {
	time.Sleep(time.Millisecond * 400)

	// If the context time expires, return false and an error
	if ctx.Err() == context.DeadlineExceeded {
		return false, errors.New("context timeout exceeded")
	}

	return true, nil
}

func main() {
	// Define variable which dictates the timeout time for the context (ctx)
	ctxTimeoutTime := 200 // in ms

	// Define an empty context
	parentCtx := context.Background()

	// Using WithTimeout,
	// create a context which is automatically cancelled after some time
	ctx, cancel := context.WithTimeout(parentCtx, time.Millisecond * ctxTimeoutTime)

	defer cancel() // Close the context at the end of main function

	userId := 42 // Define userId

	// Calling 3rd-Party API
	// Pass in the context
	isUserSubbed, err := callThirdPartyApi(ctx, userId)
	if err != nil {
		log.Fatalf("error fetching user status for User %d\nerror: %s", userId, err)
	}

	if isUserSubbed {
		fmt.Printf("this user is subbed: %d", userId)
	}
}
```

```go
// https://www.youtube.com/watch?v=kaZOXRqFPCw

package main

import (
	"context"
	"fmt"
	"log"
	"time"
)

func main() {
	start := time.Now() // Start a timer

	ctx := context.Background() // Create an empty context
	userId := 10

	// Invoke the wrapper function which calls the 3rd party API function
	val, err := fetchUserData(ctx, userId)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("result: ", val)
	fmt.Println("took: ", time.Since(start)) // Stop timer
}

// This response struct corresponds to return values of the fetchUserData function
type Response struct {
	value int
	err   error
}

// Create a wrapper function which invokes the 3rd party function
func fetchUserData(ctx context.Context, userId int) (int, error) {
	// Create a new context w/ a timeout when getting ready to invoke a 3rd party function
	maxContextTimeout := time.Duration(200) // in ms
	ctx, cancel := context.WithTimeout(ctx, time.Millisecond*maxContextTimeout)
	defer cancel() // defer the invocation of the cancel method to the end of the parent method

	// Create a new channel of type Response
	respCh := make(chan Response)

	// Spin off new goroutine
	go func() {
		// Invoke the 3rd party function
		val, err := fetchThirdPartyStuffWhichCanBeSlow()

		// Pass in the data from the 3rd party fetch function into the channel
		// so that the parent function (fetchUserData) can get this data
		respCh <- Response{
			value: val,
			err:   err,
		}
	}()

	// Run an infinite loop to check for either of the following 2 cases
	for {
		// Block until one of 2 cases is executed
		select {
		case <-ctx.Done():
			// Executes when context timeout has been exceeded or manually cancelled (via cancel())
			return 0, fmt.Errorf("fetching data from third part took too long")
		case resp := <-respCh:
			// Executes when a response from the 3rd party function is received and passed into the channel
			return resp.value, resp.err
		}
	}
}

func fetchThirdPartyStuffWhichCanBeSlow() (int, error) {
	// If this time is > ctx timeout, the fetchUserData wrapper will error
	time.Sleep((time.Millisecond * 500))
	return 999, nil
}

```

### Context with Cancellation

1. Use `context.WithCancel(<parentContext>)` to create a new context with a `cancel()` function
2. Call `cancel()` to cancel the context
3. Check `ctx.Err()` to see if the context has been cancelled

```go
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	// create a context that can be cancelled
	ctx, cancel := context.WithCancel(context.Background())

	go func() {
		// simulate work
		time.Sleep(2 * time.Second)
		// cancel the context
		cancel()
	}()

	// block until context done or 5 seconds timeout
	select {
	case <-ctx.Done():
		// Executed if context is done (either cancelled, deadline exceeded, etc.)
		fmt.Println("Context cancelled:", ctx.Err())
	case <-time.After(5 * time.Second):
		// Executed if no other cases are ready after time duration of 5 seconds
		fmt.Println("Timeout")
	}
}
```

## Context Best Practices & Conventions

1. **A context variable is typically named `ctx`**
2. **Pass as the first parameter:** The `ctx` should be the 1st parameter of a function, and it should be passed through the call stack
3. **Do not store `context.Context` in a struct:** Contexts should not be stored inside a struct type, but instead passed to each function that needs them
4. **Use it to manage cancellation and timeouts:** Use contexts to manage the lifecycle of your functions especially in asynchronous or concurrent scenarios
5. **Release Resources:** Make sure to call `cancel()` or `defer cancel()` to release resources even if you're not using it to cancel the operation

## Resources / References

- [Go Docs - Context](https://pkg.go.dev/context)
- [Go by Example: Context](https://gobyexample.com/context)
- [Melkey - Golang Context Explained - How To Use with Timeout](https://www.youtube.com/watch?v=fXzzF5y6UEU)
- [Anthony GG - How to Use the Context Package in Golang?](https://www.youtube.com/watch?v=kaZOXRqFPCw)
- [Anthony GG - A Read Use Case of Goroutines with Package Context in Golang](https://www.youtube.com/watch?v=5QyxqhLnKmM)
