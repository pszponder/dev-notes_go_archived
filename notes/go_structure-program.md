# Structure of a Go Program

```go
// Declaration of the package to which the file belongs to
package main

// Import a package from the standard library to use it in your program
import "fmt"

// main function, the entry point of the program
func main() {
	fmt.Println("Hello World!")
}
```

1. `package main` lets the Go compiler know that this program should be compiled and run as a standalone program, as opposed to being a library that's imported by other programs.
2. `import fmt` imports the `fmt` (formatting) package from the Go standard library.
3. `func main()` defines the `main` function which is the entry point for a Go program.

## The "package" keyword

In Go, the `package` keyword is used to define the package to which the Go source file belongs to.

```go
// mathutils.go
package mathutils

func Square(x int) int {
    return x * x
}
```

```go
// main.go
package main

import (
    "fmt"
    "your-module-path/mathutils" // Update this to your actual module path
)

func main() {
    num := 5
    squared := mathutils.Square(num)
    fmt.Printf("The square of %d is %d\n", num, squared)
}

```

### package main

> When you create a Go program that you intend to compile and run as a standalone executable, it must have a `package main` and associated `func main()` function.

```go
package main

// import statements here...

func main() {
	// All logic for project goes here...
}
```

`package main` has special significance in Go:

1. **Entry Point**: The `main` package serves as the entry point for executable programs. The Go runtime looks for a special function called `func main()` within the `main` package. This function is where the execution of your program starts when you run the executable.
2. **Executable Output**: When you build a Go program with a `main` package, the Go compiler generates an executable file. This executable file can be run directly from the command line without any additional steps.
3. **No Library Usage**: The `main` package is not meant to be imported or used as a library by other programs. It's specifically designed to create standalone executable programs.

> `package main` in Go is used for creating standalone executable programs. It generates an executable file that can be run directly.
