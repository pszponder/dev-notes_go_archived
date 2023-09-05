# Functions in Go

> NOTE: Go does not allow code to be written directly in the global scope. Instead, all executable code must be enclosed within a function.

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
- The variadic parameter is a [collection](go_data-types_collections.md)

```go
// r1, ...rN = return1, ...returnN
// rT1, ...rTN = returnType1, ...returnTypeN

func(keys ...keysType) (r1 rT1, r2 rT2, ...rN rTN) {
  // function body
}
```

```go
package main

import "fmt"

// Variadic function that calculates the sum of integers
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

    fmt.Println("Result 1:", result1) // Result 1: 6
    fmt.Println("Result 2:", result2) // Result 2: 150
    fmt.Println("Result 3:", result3) // Result 3: 0
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

## Resources / References

- [codecademy - functions](https://www.codecademy.com/resources/docs/go/functions)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [Anthony GG - Everythin You Need To Know About Pointers in Golang](https://www.youtube.com/watch?v=mqH21m0MsWk&list=PL0xRBLFXXsP7-0IVCmoo2FEWBrQzfH2l8&index=7)
