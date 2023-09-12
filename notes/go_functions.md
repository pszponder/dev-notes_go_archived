# Functions in Go

> NOTE: Go does not allow code to be written directly in the global scope. Instead, all executable code must be enclosed within a function. (You can still declare variables globally)

> NOTE: Function calls in Go are "pass by value". This means that any argument passed to a function is copied (instead of a reference being copied)

## Function Syntax

```go
// p1, ...pN = parameter1, ... parameterN
// t1, ...tN = type1, ...typeN
// r1, ...rN = return1, ...returnN
// rT1, ...rTN = returnType1, ...returnTypeN

func funcName(p1 t1, p2 t2, ...pN tN) (r1 rT1, r2 rT2, ...rN rTN) {

	// Code ...

	return returnValue // (if returnType is not `void`)
}
```

```go
// If no return value, don't specify return type
// it is assumed to be void
func printMessage(message string) {
	fmt.Println(message)
}

// You can omit name of return from function declaration
// but then you have to specify it within the return statement of the function body
func sayHello() string {
  return "Hello"
}

// If you include the name of values to return
// you don't need to specify them in the function body's return statement
// This function returns "Bye!" when executed
func sayGoodbye() (message string) {
  message = "Bye!"
  return
}

// Parenthesis around return type can be omitted
// if only on value returned by function
func double(a int) int {
  return a * 2
}

// Functions can accept have one or more parameters
func subtract(a int, b int) int {
	return a - b
}

// If all parameters are same type,
// only need to include param type once
func add(a, b int) int {
	return a + b
}

// Function parameters can have different types
func printStringMultipleTimes(n int, text string) {
	for i := 0; i < n; i++ {
		fmt.Println(text)
	}
}

// Function can return multiple values
func multipleReturns(a, b int) (int, int) {
	return a + b, a - b
}

// This function will return c and d since it's
// specified in the return of the function definition
func MultipleReturns2(a int64, b int64) (c int64, d int64) {
  x, y := a+b, a-b
  c = x
  d = y

  // Return names don't need to be specified if using named return values in func definition
  return
}

// Return multiple values of different types
func multipleReturns3(n int, text string) (int, string) {
	return n*2, text + text
}
```

## Functions as Parameters & Arguments

Functions can accept other functions as parameters and arguments.

```go
// For simple functions, can define function parameters in-line
// 1st parameter (fn) is function type (`func(int) int`)
func applyFunc(fn func(int) int, value int) int {
	return fn(value)
}

// Sample function to be passed as a parameter into applyFunc
func double(x int) int {
  return x * 2
}

// Invoke applyFunction,
// passing the "double" function as a parameter to "applyFunction"
result = := applyFunction(double, 5)
```

> For more complex functions, declare the [function type](go_data-types_functions.md) outside of the parent function

```go
package main

import (
	"fmt"
	"math"
)

// Function type for transformation functions
type TransformFunc func(float64) float64

// Higher-order function that applies a transformation function to each element of a slice
func transformSlice(nums []float64, fn TransformFunc) []float64 {
	result := make([]float64, len(nums))
	for i, num := range nums {
		result[i] = fn(num)
	}
	return result
}

// Transformation function: square root
func squareRoot(x float64) float64 {
	return math.Sqrt(x)
}

func main() {
	numbers := []float64{1.0, 4.0, 9.0, 16.0}

	// Applying the squareRoot function to each element of the slice
	sqrtNumbers := transformSlice(numbers, squareRoot)

	fmt.Println("Original Numbers:", numbers)
	fmt.Println("Square Roots:", sqrtNumbers)
}
```

## Returning Functions

Functions can return functions!

```go
package main

import "fmt"

// Function that returns a greeting function
func getGreetingFunction(greeting string) func(string) string {
	return func(name string) string {
		return greeting + ", " + name + "!"
	}
}

func main() {
	// Get a greeting function for "Hello"
	helloGreeting := getGreetingFunction("Hello")

	// Get a greeting function for "Hi"
	hiGreeting := getGreetingFunction("Hi")

	// Use the greeting functions
	fmt.Println(helloGreeting("Alice"))
	fmt.Println(hiGreeting("Bob"))
}
```

> For more complex functions, declare the [function type](go_data-types_functions.md) outside of the parent function

```go
package main

import "fmt"

// Function type for a binary operation function
type BinaryOperation func(int, int) int

// Function that returns a binary operation function
func getBinaryOperation(op string) BinaryOperation {
	if op == "add" {
		return func(a, b int) int {
			return a + b
		}
	} else if op == "subtract" {
		return func(a, b int) int {
			return a - b
		}
	} else {
		return nil
	}
}

func main() {
	addFunc := getBinaryOperation("add")
	subtractFunc := getBinaryOperation("subtract")

	if addFunc != nil {
		result1 := addFunc(10, 5)
		fmt.Println("Addition Result:", result1)
	}

	if subtractFunc != nil {
		result2 := subtractFunc(10, 5)
		fmt.Println("Subtraction Result:", result2)
	}
}
```

## Anonymous Functions

A function declared without a name is an `anonymous function` (a.k.a. `lambda function` or `function literal`).

- Typically, an anonymous function is assigned to a variable

```go
// p1, ...pN = parameter1, ... parameterN
// t1, ...tN = type1, ...typeN
// r1, ...rN = return1, ...returnN
// rT1, ...rTN = returnType1, ...returnTypeN

func(p1 t1, p2 t2, ...pN tN) (r1 rT1, r2 rT2, ...rN rTN) {

	// Code ...

	return returnValue // (if returnType is not `void`)
}
```

```go
// Declaring and using an anonymous function

// Declare an anonymous function and assign it to a variable
sum := func(a, b int) int {
	return a + b
}

// Use the anonymous function
result := sum(3, 5)
fmt.Println("Sum:", result)
}
```

### Immediately Invoked Anonymous Functions

You can immediately invoke an anonymous function as soon as it is declared.

Simply, add a set of parenthesis at the end of the anonymous function declaration and pass in any applicable arguments.

```go
package main

import "fmt"

func main() {
	result := func(x, y int) int {
		return x + y
	}(3, 5) // Immediately invoked with arguments 3 and 5

	fmt.Println("Result:", result)
}
```

## Variable number of parameters w/ Variadic Functions

`Variadic functions` accept a variable number of arguments of the same type.

- The `...` ellipsis syntax is used to indicate that a function parameter is variadic.
- The variadic parameter is collected into a [slice](go_data-types_slices.md)

```go
// r1, ...rN = return1, ...returnN
// rT1, ...rTN = returnType1, ...returnTypeN
// keys is a slice of type keysType

func(keys ...keysType) (r1 rT1, r2 rT2, ...rN rTN) {
  // function body
}
```

```go
package main

import "fmt"

// Variadic function that calculates the sum of integers
// numbers becomes a slice of int type
func sum(numbers ...int) int {
	total := 0

	// Iterate through the collection of numbers
	//  passed into the function as numbers param
	for _, num := range numbers {
		total += num
	}
	return total
}

func main() {
	// Calling the sum function with different number of arguments
	result1 := sum(1, 2, 3)
	result2 := sum(10, 20, 30, 40, 50)
	result3 := sum()

	// Use a slice and deconstruct variables in function
	numbers := []int{1, 2, 3, 4}
	result4 := sum(numbers...)

	fmt.Println("Result 1:", result1) // Result 1: 6
	fmt.Println("Result 2:", result2) // Result 2: 150
	fmt.Println("Result 3:", result3) // Result 3: 0
	fmt.Println("Result 4:", result4) // Result 4: 10
}
```

```go
package main

import "fmt"

// Variadic function that concatenates strings
func concatenateStrings(separator string, values ...string) string {
	result := ""

	// Iterate through the collection of string values
	//  passed into the function as values param
	for _, value := range values {
		result += value + separator
	}
	return result
}

func main() {
	concatenated := concatenateStrings(", ", "Hello", "World", "Go")
	fmt.Println("Concatenated:", concatenated) // Concatenated: Hello, World, Go,
}
```

## Working with Pointers & Functions

In Go, you can work with [pointers](go_data-types_pointer.md) in functions by passing them as arguments and receiving them as parameters.

- This allows you to modify the original values that the pointers point to, even within the function's scope

### Passing Pointers to Functions

Passing a pointer to a function passes a reference to the memory location where the actual value is stored.

- This enables the function to modify the value at that memory address

```go
package main

import "fmt"

// Function that modifies the value through a pointer
func modifyValueThroughPointer(ptr *int) {
	*ptr = 100
}

func main() {
	num := 42
	fmt.Println("Before:", num) // Before: 42

	// Notice use of "&" to pass pointer reference as argument
	modifyValueThroughPointer(&num)
	fmt.Println("After:", num) // After: 100
}
```

### Returning Pointers from Functions

A function can also return a pointer

- Useful when creating a new value within function and passing a reference to the function's value back to the caller

```go
package main

import "fmt"

// Function that creates and returns a pointer to an int
func createAndReturnPointer() *int {
	value := 42
	return &value
}

func main() {
	ptr := createAndReturnPointer()
	fmt.Println("Value at pointer:", *ptr) // Value at pointer: 42
}
```

## Receiver Functions (Type Methods)

A `Receiver Function` (a.k.a. `method`) is a function associated with a type.

- `Receiver Function`s allow you to associate behavior or functionality with a custom-defined type
- This is similar to methods in object-oriented programming languages
- `Receiver Functions` can be associated with any type (except pointers and interfaces), but it's most commonly used with [`structs`](go_data-types_structs.md)

There are two types of receiver functions

- `Pointer Receivers` - Can modify a struct (accept and work with pointers to the struct)
- `Value Receivers` - Can't modify a struct (since they accept structs as values)
- Use of `Pointer Receivers` is more common than `Value Receivers`

```go
func (r ReceiverType) FunctionName(parameters) returnType {
	// Function body
}

// r is variable used to reference the type instance in func body
// ReceiverType - Type that method is associated with
//  ReceiverType can optionally be a Pointer (*ReceiverType)
// FunctionName - Name of the method
// parameters   - Input parameters for the method
// returnType   - type that the method returns
```

Example of receiver function (Value Receiver):

```go
package main

import "fmt"

type Person struct {
	FirstName string
	LastName  string
}

// Receiver function associated with the Person type
func (p Person) GetFullName() string {
	return p.FirstName + " " + p.LastName
}

func main() {
	person := Person{"John", "Doe"}
	fullName := person.GetFullName()
	fmt.Println(fullName) // Output: John Doe
}
```

Example of Regular Function:

```go
type Coordinate struct {
	X, Y int
}

func shiftBy(x, y int, coord *Coordinate) {
	coord.X += x
	coord.Y += y
}

coord := Coordinate{5, 5}
shiftBy(1, 1, &coord) // (6, 6)
```

Same Function using Receiver Function (Pointer Receiver):

```go
type Coordinate struct {
	X, Y int
}

// Define receiver function for Coordinate type
// This function is invoked using dot notation
// by an instance of the Coordinate type
// NOTE that we pass in a pointer to Coordinate in this example
func (coord *Coordinate) shiftBy(x, y int) {
	coord.X += x
	coord.Y += y
}

coord := Coordinate{5, 5}

// Instead of passing in Coordinate pointer into function,
// we use dot notation on the Coordinate instance (coord)
// to invoke the shiftBy function
coord.shiftBy(1, 1) // (6, 6)
```

Additional Receiver Function (Value Receiver):

```go
type Coordinate struct {
	X, Y int
}

// This receiver function does not reference a pointer to Coordinate
// So a copy of the Coordinate type is passed to the function
// Therefore, need to return the coordinate
func (a Coordinate) ShiftDist(b Coordinate) Coordinate {
	return Coordinate{b.X - a.X, b.Y - a.Y}
}

firstCoord := Coordinate{2, 2}
secondCoord := Coordinate{1, 5}

// Use dot-notation to invoke ShiftDist method
// on Coordinate Type instance (firstCoord)
distance := firstCoord.ShiftDist(secondCoord) // {-1 3}
```

```go
package main

import "fmt"

type Player struct {
	health int
}

// Define a Receiver Function (Pointer Receiver)
// This Receiver function references an instance of the Player struct via a pointer
// Any variable of type Player can invoke this method using dot-notation
func (player *Player) takeDamage(dmg int) {
	fmt.Println("Player is taking damage...", -dmg)
	player.health -= dmg
	fmt.Println("Player's current health:", player.health)
}

// This "regular" function works the same way as the takeDamage receiver function
// except instead of using dot-notation, you have to invoke the function
// and pass in a reference to the Player type variable
func takeDamage(player *Player, dmg int) {
	fmt.Println("Player is taking damage...", -dmg)
	player.health -= dmg
	fmt.Println("Player's current health:", player.health)
}

func main() {
	// Instantiate a Player struct
	player := Player{health: 100}
	fmt.Println("Player's starting health:", player.health)

	// Invoke the receiver function on the player struct
	// NOTE that we use dot-notation to invoke the takeDamage receiver function (method)
	// The receiver function is invoked by the player struct (Player type)
	player.takeDamage(50) // Player health after function invocation is 50

	// Invoke the takeDamage function (regular) and pass in a reference
	// to the player struct via a pointer
	takeDamage(&player, 50) // Player health after function invocation is 0
}
```

## Deferring function execution w/ defer keyword

The `defer` keyword is used to ensure that a function call (usually for cleanup tasks) is executed later in a program's execution, specifically just before the surrounding function (in which the `defer` was written) returns.

The primary use of `defer` is to simplify function that perform various clean-up actions, like closing files, releasing resources, or unlocking mutexes.

> `defer` helps in writing cleaner and more readable code by ensuring that cleanup or finishing tasks are always executed

How `defer` works:

- Function calls with the `defer` keyword begin executing just before the parent function returns.
- **Order of Execution:** Deferred function calls are executed in Last in First Out (`LIFO`) order. That means if you have multiple `defer` statements, the last one will be executed first (think of a stack).
- **Function Arguments Evaluation**: Arguments of a deferred function are evaluated when the `defer` is executed, now when the actual deferred function is run.
- **Runs even after an Error:** A deferred function will run regardless of how the surrounding function exits, whether it exits normally or due to a panic (an unhandled runtime error in Go)

```go
/* ================================================ */
/* Basic example of using `defer` for file handling */
/* ================================================ */

func readFile(filename string) {
	file, err := os.Open(filename)
	if err != nil {
		log.Fatal(err)
	}

	// This will ensure that the file is closed
	// after all operations on it are done.
	defer file.Close()

	// ... other operations on the file ...
}

/*
Even if an error occurs during the "other operations of the file",
the `defer` statement guarantees that `file.Close()`
will be executed before `readFile` returns and exits.
*/
```

```go
/* ================== */
/* Order of Execution */
/* ================== */

// Stack the invocations of fmt.Println()
// Deferred calls are stacked and executed in reverse order
func printNumbers() {
	for i := 1; i <= 3; i++ {
		defer fmt.Println(i)
	}
}

// When calling printNumbers(), the output will be:
// 3
// 2
// 1
```

```go
/* ======================= */
/* Evaluation of Arguments */
/* ======================= */

func printLater(a int) {
	defer fmt.Println(a)
	a *= 2
	return
}

// When calling printLater(10), the output will be:
// 10

/*
Even though "a" is modified after the "defer" statement,
the deferred function prints the value of "a" as it was
when the "defer" statement was executed.
*/
```

### Handling panics w/ defer

A deferred function can be used in conjunction with the built-in [`recover()`](go_error-handling.md#recover) function to handle or log panics gracefully.

```go
/*
In this example:
- Define a deferred anonymous function inside `riskyOperation()`
- The `recover` function inside the deferred
  function captures the panic.
- The deferred function logs the panic,
  allowing the program to continue its execution
  gracefully without crashing
- As a result, even after the panic in `riskyOperation()`,
  the `main` function continues executing its next statement
*/

package main

import "fmt"

func riskyOperation() {
	// This anonymous function is deferred,
	// meaning it will be the last to run
	// within the scope of riskyOperation,
	// even if a panic occurs.
	defer func() {
		// recover() is a built-in function that
		// retrieves the value passed to panic()
		// if the current function is panicking.
		// If not, it returns nil.
		if r := recover(); r != nil {
			// Print the recovered panic value.
			fmt.Println("Recovered from panic:", r)
		}
	}()

	// Start of the risky operation.
	fmt.Println("Starting risky operation...")

	// Introduce a panic (a runtime error).
	panic("Something went wrong!")

	// This line won't be executed because of the panic above.
	fmt.Println("Finished risky operation.")
}

func main() {
	riskyOperation() // Call the function that contains a panic.

	// Because the panic was recovered in riskyOperation(),
	// the program doesn't crash,
	// and this line will be executed normally.
	fmt.Println("Continuing execution after riskyOperation...")
}

/*
Program Output:

Starting risky operation...
Recovered from panic: Something went wrong!
Continuing execution after riskyOperation...
*/
```

## init function

The `init` function is a special function that gets executed when a [package](go_packages.md) is initialized.

The primary purpose of the `init` function is to perform any package-level initialization that is required before the program starts executing.

- **CAUTION:** The `init` function is not meant for general program logic, but rather for package-level setup.

`init` function key points:

- **Execution Order:**
  - `init` functions are executed in the order they are defined within a single package
- **Initialization Order:**
  - If a package contains an `init` function, Go ensures that it is called before the `main` function of the package or any other functions within that package.
  - The `init` function is executed only once, regardless of how many times the package is imported.
- **Multiple `init` functions:**
  - A package can contain multiple `init` functions, and they will be executed in the order in which they are declared.
  - The order in which `init` functions from different packages are executed is not guaranteed.
  - So if order matters, it's best to keep `init` functions in a single file
- **No parameters or return values:**
  - The `init` function has no parameters, and it doesn't return any values.
  - The `init` function cannot be called explicitly by your code; Go's runtime system automatically calls it.
- **Common Use Cases:**
  - Setting up global variables
  - Initializing data structures
  - Configuring Settings
  - Performing one-time setup operations for a package

```go
package mypackage

import "fmt"

var importantData int

func init() {
	// This code will run once when the package is imported
	importantData = 42
	fmt.Println("Initializing importantData in mypackage")
}
```

```go
/* ============================= */
/* Initializing Global Variables */
/* ============================= */

package main

import "fmt"

var globalVar string

// This function is executed before the main function executes
func init() {
	globalVar = "Some complex value"
}

func main() {
	fmt.Println(globalVar) // Outputs: Some complex value
}
```

```go
/* ============================= */
/* Checking Essential Conditions */
/* ============================= */

// Can use `init` to check some conditions or configuration
// that are essential for the rest of the application

package main

import (
	"fmt"
	"os"
)

func init() {
	// Check if environmental variables are defined
	// if not, exit the program without continuing
	if os.Getenv("APP_ENV") == "" {
		fmt.Println("APP_ENV not set. Exiting!")
		os.Exit(1)
	}
}

func main() {
	// Rest of the application
}
```

```go
/* =================== */
/* Database Connection */
/* =================== */

// If your package communicates with a database,
// you can set up a database connection in the `init` function

package main

import (
	"database/sql"
	"log"
)

var db *sql.DB

func init() {
	var err error
	db, err = sql.Open("mysql", "user:password@/dbname")
	if err != nil {
		log.Fatalf("Failed to connect to database: %v", err)
	}
}

func main() {
	// rest of the package code
}
```

```go
/* ======================= */
/* Multiple init functions */
/* ======================= */

// A package or file can have multiple `init` functions
// they are called in the order they appear

package main

import "fmt"

func init() {
	fmt.Println("First init function.")
}

func init() {
	fmt.Println("Second init function.")
}

func main() {
	fmt.Println("Main function.")
}

// Output:
// First init function.
// Second init function.
// Main function.
```

### init() and main() functions

If an `init()` function is declared inside the `main` package, it is invoked before the `main()` function.

Order of Execution for `init()` functions and the `main()` function:

1. All imported packages or initialized. For each package:
   1. All package-level variables are initialized.
   2. `init()` functions are called in the order they are defined in the package.
2. Once all imported packages are initialized and after all `init()` functions in the `main` package have run, the `main()` function is then called.

## Resources / References

- [codecademy - functions](https://www.codecademy.com/resources/docs/go/functions)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Anthony GG - Everything You Need To Know About Pointers in Golang](https://www.youtube.com/watch?v=mqH21m0MsWk&list=PL0xRBLFXXsP7-0IVCmoo2FEWBrQzfH2l8&index=7)
