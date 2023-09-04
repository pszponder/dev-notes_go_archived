# Function Type in Go

For more information on how to work with functions, refer to [these notes on functions](go_functions.md).

## The Function Type

A function type defines a specific function signature / structure.

A function type defines:

- A set of input parameter types
- Return value type(s)

The syntax for defining a function type is similar to a function declaration, but without the function body.

```go
// t1, ...tN = type1, ...typeN
// rT1, ...rTN = returnType1, ...returnTypeN
type myFuncType func(t1, t2, ...tN) (rT1, rT2, ... rTN)
```

```go
package main

import "fmt"

// Function type for a transformation function
type TransformFunc func(float64) float64

// Transformation function: square root
func squareRoot(x float64) float64 {
    return x * x
}

func main() {
    var fn TransformFunc // Declare a variable of type TransformFunc

    // Assign the squareRoot function to the variable
    fn = squareRoot

    // Use the assigned function
    result := fn(5.0)
    fmt.Println("Result:", result)
}
```

```go
package main

import "fmt"

// Function type for a function that returns multiple values
type MultiReturnFunc func(int) (int, string)

// Sample function
func processValue(x int) (int, string) {
    return x * 2, fmt.Sprintf("Processed value: %d", x)
}

func main() {
    // Declare a variable of type MultiReturnFunc
    var fn MultiReturnFunc

    // Assign the processValue function to the variable
    fn = processValue

    // Use the assigned function
    result1, result2 := fn(5)
    fmt.Println("Result 1:", result1)
    fmt.Println("Result 2:", result2)
}
```

## Resources / References
