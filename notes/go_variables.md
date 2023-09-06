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

```go
// Declare a constant declaration block
const (
    Pi = 3.14159
    Gravity = 9.81
    Name = "Stiv"
)
```

## iota identifier (~ enums)

`iota` is a special pre-declared identifier that is used within a constant declaration block.

- `iota` represents successive integer constants that increment by 1 with each new constant declaration within the block
- `iota` is commonly used to simplify the definition of sequences of related constants, such as `enums`
- **NOTE:** When you define constants within a block using `iota`, the type of each constant is inferred from the type of the first constant in the block. (So subsequent constants within the same block will inherit the same type as the constant specified with `iota`)

### General use of iota

1. The 1st time `iota` appears in a constant declaration block, it has the value `0`
2. Each subsequent constant within the same block increments the value of `iota` by 1 (without needing to re-declare `iota`)

```go
const (
    Sunday = iota // 0
    Monday        // 1 (implicitly incremented)
    Tuesday       // 2 (implicitly incremented)
    Wednesday     // 3 (implicitly incremented)
    Thursday = 42 // 42 (overrides the implicit increment)
    Friday        // 43 (implicitly incremented)
    Saturday      // 44 (implicitly incremented)
)
```

```go
type CharacterClass int

// WITHOUT iota
const (
    Fighter CharacterClass = 0
    Wizard CharacterClass = 1
    Rogue CharacterClass = 2
    Druid CharacterClass = 3
    Ranger CharacterClass = 4
)

// WITH iota (long form)
const (
    Fighter CharacterClass = iota // 0
    Wizard  CharacterClass = iota // 1
    Rogue   CharacterClass = iota // 2
    Druid   CharacterClass = iota // 3
    Ranger  CharacterClass = iota // 4
)

// WITH iota (short form)
const (
    Fighter CharacterClass = iota // 0
    Wizard // 1 (implicitly incremental)
    Rogue  // 2 (implicitly incremental)
    Druid  // 3 (implicitly incremental)
    Ranger // 4 (implicitly incremental)
)
```

### Using iota as enums

**NOTE:** When working with `iota` as enums, it is a good idea to use a [receiver function](go_functions.md#receiver-functions-type-methods) to help work with / interpret the enum

```go
package main

import "fmt"

// Define a type for directions
type Direction int

// Define the iota constants
const (
    North Direction = iota
    East
    South
    West
)

// String returns the name of the direction as a string
func (d Direction) String() string {
    switch d {
    case North:
        return "North"
    case East:
        return "East"
    case South:
        return "South"
    case West:
        return "West"
    default:
        return "Unknown"
    }
}

func main() {
    // Create a variable to represent a direction
    currentDirection := East

    // Get and display the name of the current direction
    fmt.Printf("Current direction: %s\n", currentDirection.String())

    // Change the direction
    currentDirection = South

    // Get and display the name of the updated direction
    fmt.Printf("Updated direction: %s\n", currentDirection.String())
}
```

Alternatively, can have the receiver function use a slice or array instead of a switch statement

```go
package main

import "fmt"

// Define a type for directions
type Direction int

// Define the iota constants
const (
    North Direction = iota
    East
    South
    West
)

// String returns the name of the direction as a string
// NOTE: Order of strings inside slice/array
//  must be same as order defined in constant block
func (d Direction) String() string {
    return []string{"North", "East", "South", "West"}[d]
}

func main() {
    // Create a variable to represent a direction
    currentDirection := East

    // Get and display the name of the current direction
    fmt.Printf("Current direction: %s\n", currentDirection.String())

    // Change the direction
    currentDirection = South

    // Get and display the name of the updated direction
    fmt.Printf("Updated direction: %s\n", currentDirection.String())
}
```

```go
package main

import "fmt"

// Define an Operation type (integer)
type Operation int

// Declare the constant block with iota
const (
	Add      Operation = iota // 0
	Subtract                  // 1
	Multiply                  // 2
	Divide                    // 3
)

// Define receiver function calculate which will operate on an instance of the Operation type
func (op Operation) calculate(a, b int) int {
	switch op {
	case Add:
		return a + b
	case Subtract:
		return a - b
	case Multiply:
		return a * b
	case Divide:
		return a / b
	default:
		panic("Unhandled Operation")
	}
}

func main() {
	add := Operation(Add) // Convert type Add (int) into Operation type (also int)
	//add := Add // Alternative to above
	fmt.Println(add.calculate(2, 2)) // = 4

	sub := Operation(Subtract) // Convert type Subtract (int) into Operation type (also int)
	//sub := Subtract                   // Alternative to above
	fmt.Println(sub.calculate(10, 3)) // = 7

	mul := Operation(Multiply) // Convert type Multiply (int) into Operation type (also int)
	//mul := Multiply                  // Alternative to above
	fmt.Println(mul.calculate(3, 3)) // = 9

	div := Operation(Divide) // Convert type Divide (int) into Operation type (also int)
	//div := Divide           // Alternative to above
	fmt.Println(div.calculate(100, 2)) // = 50
}

```

### Skipping Values with iota

You can use the underscore `_` to force a skip of a value within the `iota` sequence

```go
// Use `_` to skip values when using `iota`
// In this example:
//  - Wizard is assigned a value of 0
//  - Druid is assigned a value of 3
//  - Ranger is assigned a value of 4
//  - Values 1 + 2 are skipped and unassigned
const (
    Wizard = iota // 0
    _             // 1 (skipped)
    _             // 2 (skipped)
    Druid         // 3
    Ranger        // 4
)
```

### Changing iota starting value

You can change the starting value of `iota` from 0 by adding a constant integer value to it

- You can also use any other mathematical computation with `iota`

```go
const (
    Wizard = iota + 5 // 5 (0 + 5)
    Warrior           // 6
    Rogue             // 7
)
```

```go
const (
    Wizard = iota + 5 * 2 - 3 // 7
    Warrior                   // 8
    Rogue                     // 9
)
```

### Using bitmask constants with iota

`iota` can be really useful when you want to do `bit shifting`

```go
package main

import "fmt"

// Define a type for file permissions using bitmasks
type Permission uint

const (
    // Permission bits
    Read Permission = 1 << iota // 1 (binary: 001)
    Write                       // 2 (binary: 010)
    Execute                     // 4 (binary: 100)
)

func main() {
    // Set file permissions for a user
    userPermissions := Read | Write

    // Check if the user has specific permissions
    hasReadPermission := userPermissions&Read != 0
    hasWritePermission := userPermissions&Write != 0
    hasExecutePermission := userPermissions&Execute != 0

    // Display the user's permissions
    fmt.Printf("User has read permission: %v\n", hasReadPermission)
    fmt.Printf("User has write permission: %v\n", hasWritePermission)
    fmt.Printf("User has execute permission: %v\n", hasExecutePermission)
}
```

```go
package main

import "fmt"

// Define a type for byte sizes
type ByteSize int

const (
    _           = iota // Ignore first value, which is 0
    KB ByteSize = 1 << (10 * iota) // 1 << (10 * 1) = 1024
    MB                         // 1 << (10 * 2) = 1048576
    GB                         // 1 << (10 * 3) = 1073741824
    TB                         // 1 << (10 * 4) = 1099511627776
    PB                         // 1 << (10 * 5) = 1125899906842624
)

func main() {
    fileSize := 2 * GB // Represent a file size of 2 gigabytes

    fmt.Printf("File size: %d bytes\n", fileSize)

    // Convert file size to human-readable format
    fmt.Printf("File size: %d GB\n", fileSize/GB)
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
