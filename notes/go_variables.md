# Variables & Constants in Go

## Creating Variables

### Single Variable Creation (one at a time)

```go
// Declare variable & specify its type,
//  also assign a value to the variable
var myVar1 int = 5

// Declare variable & assign them their value
// Data type is inferred by Go
var myVar2 = "hello"

var myVar3 int // Declare variable & specify type
myVar3 = 10 // Assign a value to the variable
```

### Compound Variable Creation (multiple at a time)

```go
// Using a single statement:
// Assign a to 1
// Assign b to 2
// Assign c to "hello"

var a, b, c, = 1, 2, "hello"
```

```go
// Using Block to Create multiple variables
var (
    a int = 1
    b int = 2
    c = "hello"
)
```

### Create and Assign Shortcut

Use the `:=` shortcut to create and create a variable and assign it a value

```go
myVar1 := 1

myVar2, myVar3 := 2, "hello"
```

## Assigning Variables to other Variables (By Value)

**NOTE:** When you assign a variable to another variable, the value of the new variable is a **COPY** of the value of the original variable.

```go
a := 1 // Assign variable to value 1
b := a // Assign new variable to value of "a" (copy of 1)

a = 2 // Reassign variable a w/ value 2

fmt.Printf(a) // 2
fmt.Printf(b) // 1
```

## Comma, ok

The `Comma, ok` idiom is a special case which allows a variable to be reassigned in a creation statement

- Use of this exception is typically seen when assigning errors

```go
// 2nd line results in an error
a := 1
var a = 2

// This is OK
// Even though b is assigned twice,
// since we are assigning multiple values in same statement, this is ok
a, b := 1, 2
c, b := 3, 4

// We can assign values to x, y, z
// and we also reassign value of err each time
// This avoid us from having to use err1, err2, err3, ...
x, err := //...
y, err := //...
z, err := //...
```

## Naming Conventions in Go

- Use `camelCase` for naming variables
- Use `PascalCase` if you wish to export the variable so that it is available outside of the package
- Make sure variable names are descriptive

```go
package mypackage

// Exported variable
var ExportedVariable int

// Unexported (private) variable
var unexportedVariable int
```

## Constants

Constants are created using the `const` keyword (instead of `var`)

- It is common to use `PascalCase` when defining constants

```go
const Pi = 3.14159
const Gravity = 9.81
const Name = "Stiv"
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
