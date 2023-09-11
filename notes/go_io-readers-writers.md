# Working with I/O (via Readers and Writers) in Go

## Intro to Readers and Writers in Go

`Reader` and `Writer` interfaces:

- Enable reading from and writing to different I/O sources (ex. files, in-memory buffers, network sockets and other network connections, etc.).
- There are multiple implementations of `Reader` and `Writer` interfaces in the standard library

## io.Reader Interface

The `io.Reader` interface is used to read / fetch data from a source, and write it to a provided buffer.

- Provides a simple/consistent way to abstract away details of underlying data source and focus on reading data

> A `Reader` takes data from its `source`, and writes the data into a buffer which is passed into the Reader's `Read()` method

The `io.Reader` interface defines a single method `Read()`:

```go
type Reader interface {
	Read(p []byte) (n int, err error)
}
```

- When `Read()` is invoked, the `Reader` reads from its source into the provided buffer `p`
- `Read()` takes a byte slice `p` and tries to fill it with data
  - `p` acts as a buffer
  - `Read()` will try to fill the `p` buffer with data from the source which the Reader is reading from
  - It's the responsibility of the caller (not the Reader) to allocate this buffer
- `Read()` returns the number of bytes read (`n`) and any error (`err`) encountered
  - **NOTE:** If `Read()` is invoked with a buffer which already contains data (ex. during a 2nd iteration of a loop), the existing data in the buffer will be overwritten starting from the beginning of the buffer slice (0th index) up to `n`
  - I.E. A buffer's contents beyond `n` bytes remain unchanged after a `Read()` call
- When there's no more data to read, `err` equals `io.EOF` (End of File)
- The returned value `n` represents the number of bytes read into the buffer (`p`) by the Reader's `Read()` method
  - `n == len(p)`
    - The buffer `p` was filled completely
    - There might still be more data in the source, you will need to call `Read()` again to determine this
      - The next call to `Read()` will pick up where it left off in terms of where to start reading from the source
  - `n < len(p)`
    - The buffer `p` was not filled completely
    - The source had less data remaining than the size of the buffer (`p`)
      - This is often seen when reading the last chunk of a file or data source
      - The next call to `Read` would likely return `io.EOF` to indicate the end of the data
    - Another reason could be due to a partial read. Some `Read()` implementations might not always fill the buffer, even if there's more data available. This behavior can be seen, for instance, when reading from certain network sources where data might arrive in chunks.
  - `n > len(p)`
    - This is impossible under correct `io.Reader` behavior
    - The `Read()` method can't read more data than the size of the provided buffer

```go
data := "Hello, World!"      // Our data source has 13 bytes.
r := strings.NewReader(data) // Create new reader w/ "data" as its source

buf := make([]byte, 5)       // Our buffer can hold 5 bytes.

n, _ := r.Read(buf)
// After the first call to Read:
// n == 5, buf contains "Hello"

n, _ = r.Read(buf)
// After the second call to Read:
// n == 5, buf contains ", Wor"

n, _ = r.Read(buf)
// After the third call to Read:
// n == 3, buf contains "ld!"
// (only the first 3 bytes are updated, last 2 remain unchanged)
// The data source had only 3 bytes remaining, so n < len(buf).
```

### Using Readers

```go
package main

import (
	"fmt"
	"io"
	"strings"
)

func main() {
	// Create a new reader from a string
	reader := strings.NewReader("SAMPLE")

	// Define a new string variable using strings.Builder type
	var newString strings.Builder

	// Create a byte slice (buffer) with a size of 4 bytes
	buffer := make([]byte, 4)

	// Start an infinite loop for reading from the reader
	for {
		// Read up to 4 bytes from the reader into the 'buffer' slice
		numBytes, err := reader.Read(buffer)

		// Extract the chunk of data that was read from the 'buffer'
		// NOTE: It is important to slice the buffer
		// by the number of bytes read by the Reader
		// otherwise, you could be extracting old data
		// from a previous iteration
		chunk := buffer[:numBytes]

		// Write the chunk to the 'newString' string builder
		newString.Write(chunk)

		// Print the number of bytes read and the content of the chunk
		fmt.Printf("Read %v bytes: %c\n", numBytes, chunk)

		// Check if the end of the input has been reached (EOF)
		if err == io.EOF {
			break // Exit the loop if EOF is encountered
		}
	}

	// Print the final concatenated string built by the string builder
	fmt.Printf("%v\n", newString.String())

	/*
	Read 4 bytes: [S A M P]
	Read 2 bytes: [L E]
	Read 0 bytes: []
	SAMPLE
	*/
}
```

```go
/* =========================== */
/* Reading from a bytes buffer */
/* =========================== */

package main

import (
	"bytes"
	"fmt"
	"io"
)

func main() {
	// Create a new bytes Reader with the string "Hello, World!"
	r := bytes.NewReader([]byte("Hello, World!"))

	// Create a buffer of size 5 to read chunks of data into
	buf := make([]byte, 5)

	// Loop until we've read the entire content
	for {
		// Try to read from the Reader into our buffer
		n, err := r.Read(buf)

		// If we've reached the end of our content, break out of the loop
		if err == io.EOF {
			break
		}

		// If there was another error while reading, print it and break out
		if err != nil {
			fmt.Println("Error reading:", err)
			break
		}

		// Print the number of bytes we read and the actual bytes
		// We only print the first 'n' bytes of the buffer, since that's how many were populated
		fmt.Printf("Read %d bytes: %s\n", n, buf[:n])
	}
}
```

```go
/* =================== */
/* Reading from a File */
/* =================== */

package main

import (
	"fmt"
	"io"
	"os"
)

func main() {
	// Open a file for reading
	file, err := os.Open("path_to_file.txt")
	if err != nil {
			panic(err)  // Stop the program and print the error if there was one
	}

	// Ensure the file gets closed when we're done
	defer file.Close()

	// Create a buffer (of size 1024 bytes in this case) to hold chunks of the file as we read it
	buf := make([]byte, 1024)

	// Loop until we've read the entire file
	for {
		// Try to read from the file into our buffer
		n, err := file.Read(buf)

		// If we've reached the end of the file, break out of the loop
		if err == io.EOF {
			break
		}

		// If there was another error while reading, print it and break out
		if err != nil {
			fmt.Println("Error reading:", err)
			break
		}

		// Print the number of bytes we read from the file in this iteration
		fmt.Printf("Read %d bytes\n", n)
	}
}
```

## io.Writer Interface

The `io.Writer` interface is used to send / write data to a destination.

- Unlike `io.Reader`, where you provide an empty buffer to be filled with data, with `io.Writer`, you provide the data, and it gets written / sent to the underlying writer's destination

> A `Writer` takes data from an array buffer passed into its `Write()` method and writes that data into the Writer's data sink.

The `io.Writer` interface defines a single method `Write()`:

```go
type Writer interface {
	Write(p []byte) (n int, err error)
}
```

- `Write()` method takes a byte slice (`p`) which contains information and writes it into the Writer's data sink
- `n` represents the number of bytes from the buffer that are successfully written to the data sink
- `err` is non-nil if any error occurs while writing to the sink

What happens when you call `Write()`?

1. Provide a buffer (`p`) with data to write to the data sink (destination)
2. The `Write()` method attempts to write as many of the bytes from the data buffer into the data sink as possible
3. After execution, `Write()` returns the number of bytes (`n`) successfully written to the data sink (destination)
4. If there's an error during the process, it returns a non-nil error (`err`)

### Using Writers

Many types in the standard library implement the `io.Writer` interface:

- `os.File` -- Allows you to write data to files
- `bytes.Buffer` -- A buffer that can grow as needed, useful for building up byte slices
- `net.Conn` -- Represents a network connection (contains both `Read()` and `Write()` methods)
- `http.ResponseWriter` -- Used in web servers to write responses back to clients
- etc.

```go
import (
	"bytes"
	"fmt"
)

func main() {
	// Create a new buffer initialized with an empty string.
	// 'bytes.Buffer' is a flexible and efficient buffer
	// for byte slices (like strings).
	buffer := bytes.NewBufferString("")

	// Attempt to write the string "SAMPLE" into the buffer.
	// The method returns the number of bytes written and any error encountered.
	numBytes, err := buffer.WriteString("SAMPLE")

	// Check if there was an error while writing to the buffer.
	if err != nil {
		// If there was an error, print it.
		fmt.Println(err)
	} else {
		// If no error occurred,
		// print the number of bytes written and the current content of the buffer.
		fmt.Printf("Wrote %v bytes: %s\n", numBytes, buffer.String())
	}
}
```

```go
package main

import (
	"bytes"
	"fmt"
	"io"
)

func main() {
	// Create a bytes.Buffer to write data to
	output := bytes.NewBuffer(nil)

	// Data to be written
	data := []byte("Hello, world!")

	// Write data from the 'data' byte slice to the 'output' buffer
	n, err := output.Write(data)

	// Check for errors
	if err != nil {
		fmt.Println("Error:", err)
	} else {
		// Print the number of bytes written and the contents of the buffer
		fmt.Printf("Wrote %d bytes.\n", n)
		fmt.Println("Buffer contents:", output.String())
	}
}
```

```go
/* ================= */
/* Writing to a File */
/* ================= */

package main

import (
	"fmt"
	"os"
)

func main() {
	// Create a new file or truncate it if it already exists.
	// 'file' is an instance that implements the io.Writer interface.
	file, err := os.Create("output.txt")
	if err != nil {
		fmt.Println("Error creating file:", err)
		return
	}
	// Ensure the file is closed after writing to prevent resource leaks.
	defer file.Close()

	// Define the data we want to write to the file.
	data := "Hello, io.Writer!"

	// Write the data to the file. This sends the bytes from 'data' to the file.
	n, err := file.Write([]byte(data))
	if err != nil {
		fmt.Println("Error writing to file:", err)
		return
	}
	// Print the number of bytes written to the file.
	fmt.Printf("Wrote %d bytes to file\n", n)
}
```

```go
/* ================== */
/* Using bytes.Buffer */
/* ================== */

package main

import (
	"bytes"
	"fmt"
)

func main() {
	// Declare a new buffer. It's a flexible byte buffer that grows as bytes are written to it.
	var buf bytes.Buffer

	// Write to the buffer using the Write method.
	// This appends the provided bytes to the end of the buffer.
	buf.Write([]byte("Hello, "))

	// Another way to write to the buffer using the WriteString method.
	// This appends the provided string to the end of the buffer.
	buf.WriteString("world!")

	// Print the content of the buffer.
	fmt.Println(buf.String()) // Outputs: Hello, world!
}
```

```go
/* ======================= */
/* Writing to HTTP Clients */
/* ======================= */

package main

import (
	"fmt"
	"net/http"
)

// Define a handler function for HTTP requests.
func helloHandler(w http.ResponseWriter, r *http.Request) {
	// Use the writer interface to send a response back to the client.
	// In this context, w is an instance that implements the io.Writer interface.
	w.Write([]byte("Hello, web client!"))
}

func main() {
	// Register the handler function for the root path.
	http.HandleFunc("/", helloHandler)

	// Start the web server on port 8080.
	fmt.Println("Starting server on :8080")
	http.ListenAndServe(":8080", nil)
}
```

## io.Reader vs io.Writer

`io.Reader`

- **Purpose:**
  - Used to read / fetch data from a source
  - You provide an empty buffer to the `Read()` method
  - The `Read()` method fills the empty buffer with data from the source
- **Buffer Handling:**
  - The buffer (`p`) you provide is like an empty vessel
  - The `Read()` method fills this buffer with data from the source
  - The number of bytes filled into the buffer is returned as `n`
  - If there's an error during reading, a non-nil error is returned
- **Typical Scenarios:**
  - When you're fetching content from a file, reading from a network socket, etc.

`io.Writer`

- **Purpose:**
  - Used to send / write data to a destination (data sink)
  - You provide the data (via `p` buffer) and `Write()` sends the data
- **Buffer Handling:**
  - The buffer (`p`) you provide contains the data you wish to write
  - The `Write()` method tries to write all or as much of this data as possible to the destination (data sink)
  - `Write()` returns the number of bytes successfully written to the data sink
- **Typical Scenarios:**
  - When you're saving content to a file, sending data over a network, writing to a buffer, etc.

## bufio Package

[Go - bufio](https://pkg.go.dev/bufio)

The `bufio` package provides buffered I/O

- No need to manually manage buffers or construct data
- Wraps `io.Reader` and `io.Writer` interfaces to provide buffered versions of them
- This can improve performance when reading/writing data in smaller chunks, by minimizing the number of system calls
- `bufio` also offers functions to read from and write to delimited data such as lines or words

```go
source := strings.NewReader("SAMPLE")
buffered := bufio.NewReader(source)
newString, err := buffered.ReadString(`\n`)
if err == io.EOF {
	fmt.Println(newString)
} else {
	fmt.Println("Oh no, something went wrong!")
}
```

### bufio.Reader

`bufio.Reader()` provides methods read data in chunks or line by line

- Used for buffered reading
- Can reduce the number of I/O operations by maintaining an internal buffer and reading from it until it's empty

```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"strings"
)

func main() {
	// Create a simple string reader.
	// This is a basic example; in real-world scenarios,
	// this could be a file, network stream, etc.
	r := strings.NewReader("Hello, bufio!\nWelcome to Go.")

	// Initialize a new buffered reader using the string reader.
	br := bufio.NewReader(r)

	for {
		// Read a line from the buffered reader
		// until the delimiter '\n' is encountered.
		// The delimiter is not stripped from the output.
		line, err := br.ReadString('\n')

		// Check if the end of the input is reached
		if err == io.EOF {
			// Print the last line if it's not empty
			if len(line) > 0 {
				fmt.Print(line)
			}
			break
		}
		// Check for any other error
		if err != nil {
			fmt.Println("Error reading line:", err)
			return
		}

		// Print the read line
		fmt.Print(line)
	}
}

```

```go
import (
	"bufio"
	"fmt"
	"io"
	"strings"
)

func main() {
	// Create a new reader instance that reads from a string "SAMPLE".
	source := strings.NewReader("SAMPLE")

	// Wrap the string reader with a buffered reader.
	// This provides methods to efficiently read the underlying
	// string in chunks or up to delimiters.
	buffered := bufio.NewReader(source)

	// Attempt to read from the buffered reader
	// until the delimiter '\n' (newline) is encountered.
	// Since our string "SAMPLE" does not have a newline,
	// the reader will read until the end of the string.
	newString, err := buffered.ReadString('\n')

	// Check if the error returned is an EOF (End of File/Stream) error,
	// which indicates that we've successfully read the string until its end.
	if err == io.EOF {
		// Print the string we read. In this case, it'll be "SAMPLE".
		fmt.Println(newString)
	} else {
		// If we received any other error, print an error message.
		// In this example,
		// it's unlikely to reach here unless there's an unusual issue with the reader.
		fmt.Println("Oh no, something went wrong!")
	}
}

```

### bufio.Writer

`bufio.Writer` provides methods to write data in chunks.

- Provides buffered writing which can be more efficient than writing directly to an underlying writer if the writes are in small chunks
- You need to ensure you call `Flush()` to clear the buffer and write any remaining data

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	// Create a new file. If the file already exists, it's truncated.
	file, err := os.Create("output.txt")
	if err != nil {
		fmt.Println("Error creating file:", err)
		return
	}

	// Ensure the file gets closed once we're done.
	defer file.Close()

	// Initialize a buffered writer using the file.
	bw := bufio.NewWriter(file)

	// Write strings to the internal buffer.
	// Data won't be written to the file until the buffer fills up
	// or Flush() is called.
	bw.WriteString("Hello, bufio.Writer!")
	bw.WriteString("\nBuffered writing in Go.")

	// Flush the buffer to ensure all buffered data is written to the file.
	err = bw.Flush()
	if err != nil {
		fmt.Println("Error flushing buffer:", err)
	}
}
```

### bufio.Scanner

[go - bufio.Scanner](https://pkg.go.dev/bufio#Scanner)

`bufio.Scanner` provides a convenient interface for reading input, especially when the structure of the input is line-by-line or delimited in some other way.

- Primary use of `bufio.Scanner` is to scan input into tokens (typically lines of text) based on a customizable split function

`bufio.Scanner` Key Points:

1. **Default Behavior:**
   1. By default, `bufio.Scanner` reads input line-by-line, which is useful for processing text files or standard input from the console where data often comes in the form of lines.
2. **Custom Split Functions:**
   1. While the default split function breaks the input into lines, `bufio.Scanner` is quite flexible.
   2. You can use one of the provided split functions or define your own.
   3. For instance, bufio provides functions like `ScanWords` (splits input into words) and `ScanBytes` (splits input into bytes).
3. **Efficiency and Simplicity:**
   1. For many tasks, using a Scanner can be both more memory efficient and simpler than reading whole lines into memory.
   2. It's designed to minimize memory allocation by scanning the input stream directly.
4. **Error Handling:**
   1. Scanner provides a unified way to check for errors after scanning, making the code cleaner, especially inside loops.
5. **Common Use Cases:**
   1. Scanner is frequently used for parsing input files, reading from the console, and other scenarios where data can be naturally tokenized.

```go
package main

import (
	"bufio"
	"fmt"
	"strings"
)

func main() {
	// Create a simple string reader as our data source.
	r := strings.NewReader("Hello, bufio!\nWelcome to Go.")

	// Initialize a new Scanner using the string reader.
	scanner := bufio.NewScanner(r)

	// The scanner will read input line-by-line by default.
	// You can change this behavior.
	// For instance, to scan words, use:
	// scanner.Split(bufio.ScanWords)

	// Read each line from the input source.
	for scanner.Scan() {
		line := scanner.Text()  // Get the scanned line as a string.
		fmt.Println(line)
	}

	// Check if any errors occurred while scanning.
	if err := scanner.Err(); err != nil {
		fmt.Println("Error scanning:", err)
	}
}
```

```go
/*
Usage in Bash:

# Ex 1.
go run main.go < input.txt

# Ex 2.
printf "Hello\nWorld\n" | go run main.go
*/

package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	// Create a new scanner that reads from the standard input
	// (typically the terminal or console).
	scanner := bufio.NewScanner(os.Stdin)

	// Initialize a slice to store the lines of input.
	// It starts with a length of 0 and a capacity of 5.
	lines := make([]string, 0, 5)

	// Continuously scan for new lines from standard input.
	// The loop will break when there's no more input or if an error occurs.
	for scanner.Scan() {
		// Append the scanned line to the 'lines' slice.
		lines = append(lines, scanner.Text())
	}

	// Check if there were any errors during scanning.
	if scanner.Err() != nil {
		// If there was an error, print it.
		fmt.Println(scanner.Err())
	}

	// Print the number of lines that were read from the input.
	fmt.Printf("Line count: %v\n", len(lines))

	// Iterate over the 'lines' slice and print each line.
	for _, line := range lines {
		fmt.Printf("Line: %v\n", line)
	}
}
```

## Resources / References

- [Go - bufio](https://pkg.go.dev/bufio)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [NerdCademy - Write Files with Go](https://www.youtube.com/watch?v=y4wyZCxLKac&list=PLujhHB_uAFJws6Vv5q1KDoaQ4YcpS9UOm&index=4)
- [NerdCademy - Learn to Read Files with Go!](https://www.youtube.com/watch?v=8uKtaYJlTzs&list=PLujhHB_uAFJws6Vv5q1KDoaQ4YcpS9UOm&index=5)
- [Explaining Common I/O Patterns in Go](https://medium.com/dev-bits/explaining-common-i-o-patterns-in-go-cd01b1b749c4)
- [Anthony GG - How to Stream Large Files Over TCP in Golang](https://www.youtube.com/watch?v=82oFmY-Qeok)
