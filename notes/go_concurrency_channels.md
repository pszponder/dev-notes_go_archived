# Channels in Go

## Intro to Channels

`Channels` enable bidirectional communication between [`goroutines`](go_concurrency_goroutines.md)

- Provide a way to send (write) and receive (read) values between goroutines safely
- A single `channel` can be used to send or receive data from multiple goroutines
- Think of `channels` as message queues (FIFO) between goroutines
- By default, `channels` are BLOCKING
  - When data is sent to a channel, the goroutine is blocked until another goroutine reads from that channel
  - When reading from a channel, the goroutine is blocked until there is data to read
- `Channels` can be used to help the [main goroutine](go_concurrency_goroutines.md#the-main-goroutine) determine when all other goroutines have been completed

**CAUTION:** If a `channel` is _FULL_, it will always block

## Channel Conventions

- When naming a channel variable, the variable should end with `ch` or `Ch`
- When setting the size of a [buffered channel](go_concurrency_channels.md#buffered-channels), use powers of 2 (ex. 2, 4, 8, 16, 32, 64, 128, 256,...)
- To avoid `deadlock` errors:
  - WRITE to a channel before READing from it
  - Close a channel when you are done writing to it

## Creating & Using Channels

To create an `channel`, use the `make()` function, specifying the `chan` keyword and the data type to be used for the channel communication

- If you don't include a 2nd argument to `make()`, then the channel will be _unbuffered_
- **NOTE:** Always _WRITE_ data before _READ_ when working with channels
- **NOTE:** To avoid a `deadlock`, make sure you write to the channel at least as many times as you read from the channel. Reading from the channel more than you write to it results in a `deadlock`.

```go
// Create a new unbuffered channel
myChannel := make(chan <dataType>)

// Pass data through a channel
// Notice that the channel is to the LEFT of the arrow
// (NOTE: This code is generally found inside a goroutine)
myChannel <- <data>

// Retrieve data from a channel
// Notice that the channel is to the RIGHT of the arrow
<-myChannel

// Retrieve data from a channel and store it as a variable
myVar := <-myChannel
```

```go
// Create an unbuffered channel
// This channel will transfer data of type int
//  to and from goroutines (the data type can be any type)
ch := make(chan int)

// NOTE: ALWAYS WRITE data to a channel BEFORE READing the data
// Send data to the channel
//  Spin off several goroutines
//  At the end of each goroutine, we can pass data to a channel
//  In this example, the goroutines are declared using IFFE
go func() { ch <- 1 }()
go func() { ch <- 2 }()
go func() { ch <- 3 }()

// Receive data from the channel and assign to variables
first := <-ch
second := <-ch
third := <-ch

fmt.Println(first, second, third)
```

## Buffered Channels

> **NOTE:** The default capacity for any channel is 0

- When creating a channel, can pass in a 2nd parameter which will cause the channel to be `buffered`
- The 2nd parameter is a number representing the number of messages that the channel can handle at the same time (the buffer's capacity)
- A buffered channel is a `FIFO` queue
- **NOTE:** Always _WRITE_ data before _READ_ when working with channels

```go
//...
func main() {
	// Create a buffered string channel
	// Since sending 2 messages,
	// should buffer capacity of channel by at least 2
	ch := make(chan string, 2)

	// Send (WRITE) messages to the channel
	ch <- "1st message"
	ch <- "2nd message"

	// Receive (READ) messages from the channel
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

## When to use Unbuffered vs Buffered Channels

### Using an Unbuffered Channel

```go
ch := make(chan int) // Unbuffered channel
```

1. **Synchronization Guarantee:**

   1. An [unbuffered channel](go_concurrency_channels.md#creating--using-channels) provides a strong synchronization guarantee between goroutines.
   2. When a value is sent on an unbuffered channel, the sending goroutine is blocked until another goroutine receives the value.
   3. Similarly, when a value is received from an unbuffered channel, the receiving goroutine is blocked until another goroutine sends a value on the channel.
   4. In essence, an unbuffered channel synchronizes the sender and receiver, ensuring that both are ready for the communication.

1. **Use Cases:**
   1. Suitable for ensuring that a sent message is immediately picked up by a receiver before the sender continues execution
   2. Useful for synchronizing the execution of goroutines.

### Using a Buffered Channel

```go
ch := make(chan int, 8) // Buffered channel with a capacity of 8
```

1. **No Immediate Synchronization Guarantee:**

   1. A buffered channel does not block the sending goroutine if there is space in the buffer, even if there is no goroutine ready to receive the value.
   2. The sending goroutine is only blocked when the buffer is full.
   3. A receiving goroutine is blocked until there is data in the buffer to be received.

2. **Use Cases:**
   1. Suitable when you want to allow goroutines to continue execution without waiting for the sent message to be received.
   2. Helps to prevent deadlock situations in scenarios where multiple goroutines are sending and receiving data, as the channel buffer can hold sent values until they are received.

### Example of Buffered vs Unbuffered Channels:

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	/**
	UNBUFFERED CHANNEL:
	- "Sent" and "Received" should print at roughly the same time
	- Shows the synchronization between sending and receiving
	*/

	unbufferedCh := make(chan int)
	go send(unbufferedCh)
	go receive(unbufferedCh)

	time.Sleep(2 * time.Second)

	/**
	BUFFERED CHANNEL:
	- "Sent" will print immediately, and "Received" will print after
	- Shows that the sending goroutine does not wait for the receiving goroutine.
	*/

	bufferedCh := make(chan int, 1)
	go send(bufferedCh)
	go receive(bufferedCh)

	time.Sleep(2 * time.Second)
}

func send(ch chan int) {
	ch <- 1
	fmt.Println("Sent")
}

func receive(ch chan int) {
	<-ch
	fmt.Println("Received")
}
```

## Iterating Over & Closing Channels

Use the `close(<ch>)` function to close a channel:

```go
// To close a channel, use the "close" function
close(myChannel) // closes a channel named "myChannel"
```

Use either a [range](go_loops.md#looping-over-iterables-with-range) or [infinite for loop](go_loops.md#infinite-loop) to iterate over a channel and read the data written to it:

```go
// OPTION 1: Range over data written to the channel
for myData := range myChannel {
	// myData represents a piece of data written to the channel
	fmt.Println(myData)
}

// OPTION 2: Loop over data written to channel
for {
	myData, ok := <-myChannel
	if !ok {
		break
	}

	// myData represents a piece of data written to the channel
	fmt.Println(myData)
}
```

Full example of writing to a channel, closing the channel, and reading from the channel.

```go
// Define a buffered channel of type string
msgCh := make(chan string, 8)

// Write some data to the channel
msgCh <- "1st message"
msgCh <- "2nd message"
msgCh <- "3rd message"

// Close the channel after you are done writing to it
// Otherwise, you may get a deadlock error
close(msgCh)

// OPTION 1: Iterate over the channel via a range loop (preferred method)
for msg := range msgCh {
	fmt.Println(msg)
}

// OPTION 2: Infinite For Loop
for {
	// Receive data from the channel and save it into "msg" variable
	// "ok" will be false when there are no more messages in the channel
	msg, ok := <-msgCh

	// Break out of infinite for loop if there are no more messages
	if !ok {
		// If ok is false, the channel is closed and drained.
		break
	}

	fmt.Println(msg)
}

fmt.Println("Completed writing to and reading from channel")
```

### When to close a channel

1. **Definite Break Point:**
   1. Close the channel when you know that there will be no more data to send on the channel.
   2. Useful to avoid deadlocks and ensure that receiving goroutines are not waiting indefinitely for data that will never come.
2. **Prevent Leaks:**
   1. Prevent goroutine leaks by closing channels when they are no longer needed.
   2. Ensures that goroutines that are waiting to receive from the channel can stop waiting and free up resources.
3. **Signaling:**
   1. Use closed channels as a signaling mechanism to indicate to other goroutines that processing is done and they should finish up.

### Data Producers & Data Consumers

Below is an example of producing and consuming messages using Go channels.

- One goroutine produces messages and sends them to a channel.
- Another goroutine consumes messages from that channel.
- Uses [WaitGroups](go_concurrency_waitgroups.md) to block the code until the goroutines return

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	// Create a buffered channel with capacity 8.
	messagesCh := make(chan string, 8)

	var wg sync.WaitGroup
	// Increment the WaitGroup counter.
	wg.Add(2) // we have two goroutines: producer and consumer

	// Start the producer goroutine.
	go producer(messagesCh, &wg)

	// Start the consumer goroutine.
	go consumer(messagesCh, &wg)

	// Wait for the counter to be decremented by both goroutines.
	// Until this happens code below is blocked
	wg.Wait()

	// Close the channel
	close(messagesCh)
}

// producer produces messages and sends them to the channel.
func producer(ch chan string, wg *sync.WaitGroup) {
	// Decrement the counter when the goroutine completes.
	defer wg.Done()

	for i := 0; i < 10; i++ {
		msg := fmt.Sprintf("Message %d", i)

		ch <- msg // Send the message to the channel.

		fmt.Println("Produced:", msg)

		// Sleep for a while before producing the next message.
		time.Sleep(time.Millisecond * 500)
	}
}

// consumer consumes messages from the channel.
func consumer(ch chan string, wg *sync.WaitGroup) {
	// Decrement the counter when the goroutine completes.
	defer wg.Done()

	// range over the channel to receive messages.
	for msg := range ch {
		fmt.Println("Consumed:", msg)

		// Sleep for a while before consuming the next message.
		time.Sleep(time.Second)
	}
}
```

## WaitGroups

[WaitGroups](go_concurrency_waitgroups.md)

## select

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Kantan Coding - Master Go Programming w/ these concurrency Patterns](https://youtu.be/qyM8Pi1KiiM?feature=shared)
- [Go by Example: Channels](https://gobyexample.com/channels)
- [Golang Dojo - Golang Concurrency Basics](https://www.youtube.com/playlist?list=PLve39GJ2D71wSwRQLp_h8B60pKgS85StC)
- [Anthony GG - Learn Golang Concurrency For Beginners By Example](https://www.youtube.com/watch?v=P4tckkcyef0)