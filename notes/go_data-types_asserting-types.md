# Asserting Types in Go

## Using fmt package to print value type

Use the `%T` [verb](https://pkg.go.dev/fmt#hdr-Printing) with `fmt.Printf()` to print the type of a value

```go
a, b, c := "yes", 20.6, false
d := []int{1, 2, 3, 4}
e := map[string]string{
	"first": "Mr.",
	"last": "Bean",
}

fmt.Printf("a is: %T\n", a) // a is: string
fmt.Printf("b is: %T\n", b) // b is: float64
fmt.Printf("c is: %T\n", c) // c is: bool
fmt.Printf("d is: %T\n", d) // d is: []int
fmt.Printf("e is: %T\n", e) // e is: map[string]string
```

## Using reflect package to get value type

You can also use the `reflect` package's `TypeOf` method to obtain the type of a value.

```go
package main

import (
	"fmt"
	"reflect"
)

func main() {
	a, b, c := "yes", 20.6, false
	d := []int{3, 4, 5, 6}
	e := map[string]string{
		"first": "Mr.",
		"last": "Bean",
	}
	fmt.Printf("type of a is %v\n", reflect.TypeOf(a))
	fmt.Printf("type of b is %v\n", reflect.TypeOf(b))
	fmt.Printf("type of c is %v\n", reflect.TypeOf(c))
	fmt.Printf("type of d is %v\n", reflect.TypeOf(d))
	fmt.Printf("type of e is %v\n", reflect.TypeOf(e))
}
```

```bash
# Output of above code
type of a is string
type of b is float64
type of c is bool
type of d is []int
type of e is map[string]string
```

## Type Switch

A type switch is a construct that allows you to test the type of an interface value against multiple types.

```go
func printType(x interface{}) {
	switch x.(type) {
	case int:
		fmt.Println("It's an int")
	case string:
		fmt.Println("It's a string")
	default:
		fmt.Println("It's something else")
	}
}

func main() {
	printType(42)          // It's an int
	printType("Hello")     // It's a string
	printType(3.14)        // It's something else
}
```

## Type Assertion (when working with interfaces)

If you have an [`interface`](go_data-types_interfaces.md) value and you want to check the value of its underlying type, you need to use a type assertion in the following form:

```go
val, ok := i.(<Type>)
```

```go
func printType(x interface{}) {
	// Check if x is an int
	if _, ok := x.(int); ok {
		fmt.Printf("It's an int: %d\n")
	} else {
		fmt.Println("It's not an int")
	}
}

func main() {
	printType(42)          // It's an int: 42
	printType("Hello")     // It's not an int
}
```

## Resources / References

- [How to check the type of a value in Go](https://freshman.tech/snippets/go/check-type-of-value/)
