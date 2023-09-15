# Generics in Go

## Generics Intro

`Generics` enable writing of functions and data structures that take and use a "generic" type.

- This enables functions or data structures to handle multiple types of data

## Generic Functions

`Generic Functions` are [functions](go_functions.md) defined using type [interfaces](go_data-types-interfaces.md) (called `type constraints` in the context of generics)

```go
// Syntax of generic function

// funcName                           OR
func MyFunc[T constraint, U constraintA | constraint B](a T, b U) T {
	//...
}
```

```go
package main

import "fmt"

// T is a type parameter that can be any type,
// as indicated by the constraint "any"
func Swap[T any](a, b T) (T, T) {
	return b, a
}

func main() {
	a, b := 3, 4
	a, b = Swap(a, b)
	fmt.Println(a, b) // Outputs: 4 3
}
```

```go
type Stringer interface {
	String() string
}

func PrintString[T Stringer](s T) {
	fmt.Println(s.String())
}
```

## Type Constraints

### Built-In Type Constraints

**NOTE:** Outside of the `any` and `comparable` type constraints which are available globally, you will have to import the `constraints` package to access the other built-in constraints.

| Constraint   | Description                                                                  |
| ------------ | ---------------------------------------------------------------------------- |
| `any`        | Any type                                                                     |
| `comparable` | Anything that can be compared for equality                                   |
| `Unsigned`   | All unsigned integers                                                        |
| `Signed`     | All signed integers                                                          |
| `Ordered`    | Sortable types (numbers, strings which can be compared using `<`, `>`, etc.) |
| `Integer`    | All integers                                                                 |
| `Float`      | All floating point numbers                                                   |
| `Complex`    | All complex numbers                                                          |

**CAUTION:** The constraints package is part of the experimental standard package. You can either import it or compose interfaces to obtain the same constraints above.

- To import, use `go get golang.org/x/exp/constraints`
- Add `import "golang.org/x/exp/constraints"`
- Access specific constraints using dot notation against `constraints` object

```go
// The constraints library under the hood
// composes multiple type constraints together
// https://cs.opensource.google/go/x/exp/+/92128663:constraints/constraints.go

// Copyright 2021 The Go Authors. All rights reserved.
// Use of this source code is governed by a BSD-style
// license that can be found in the LICENSE file.

// Package constraints defines a set of useful constraints to be used
// with type parameters.
package constraints

// Signed is a constraint that permits any signed integer type.
// If future releases of Go add new predeclared signed integer types,
// this constraint will be modified to include them.
type Signed interface {
	~int | ~int8 | ~int16 | ~int32 | ~int64
}

// Unsigned is a constraint that permits any unsigned integer type.
// If future releases of Go add new predeclared unsigned integer types,
// this constraint will be modified to include them.
type Unsigned interface {
	~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~uintptr
}

// Integer is a constraint that permits any integer type.
// If future releases of Go add new predeclared integer types,
// this constraint will be modified to include them.
type Integer interface {
	Signed | Unsigned
}

// Float is a constraint that permits any floating-point type.
// If future releases of Go add new predeclared floating-point types,
// this constraint will be modified to include them.
type Float interface {
	~float32 | ~float64
}

// Complex is a constraint that permits any complex numeric type.
// If future releases of Go add new predeclared complex numeric types,
// this constraint will be modified to include them.
type Complex interface {
	~complex64 | ~complex128
}

// Ordered is a constraint that permits any ordered type: any type
// that supports the operators < <= >= >.
// If future releases of Go add new ordered types,
// this constraint will be modified to include them.
type Ordered interface {
	Integer | Float | ~string
}
```

### Custom Type Constrains

You can pass in a custom type interface to a generic function and use it as a type constraint

```go
// This type constraint specifies the type as either
// an int32 OR a uint32
type Integers32 interface {
	int32 | uint32
}

// This generic function accepts a generic constraint
// which adheres to the Integers32 interface
// So the arr parameter can be a slice
// of either int32 OR uint32
func SumNums[T Integers32](arr []T) T {
	sum := 0
	for i := 0; i < len(arr); i++ {
		sum += arr[i]
	}
	return sum
}

// ALTERNATIVELY, instead of using the type constraint (Integers32)
// specify both types within the type parameter
func SumNums2[T int32 | uint32](arr []T) T {
	sum := 0
	for i := 0; i < len(arr); i++ {
		sum += arr[i]
	}
	return sum
}

func main() {
	numsA := []int32{1, 2, 3}
	numsB := []uint32{1, 2, 3}

	totalA := SumNums(numsA)
	totalB := SumNums(numsB)
}
```

### Custom Type Constraints and Type Aliases

Normally, you can't use type constraints with [type aliases](go_data-types_aliases-conversions.md#type-aliases-in-go).

You can use `generic approximation` within the type constraint to enable use of type aliases without generating errors

- `generic approximation` enables checking underlying types as opposed to just the type alias

```go
// The ~ (generic approximation)
// in front of the types indicate
// that a type alias which is either an int32 or uint32
// can be used and is a valid Integers32 type
type Integers32 interface {
	~int32 | ~uint32
}

func SumNums[T Integers32](arr []T) T {
	sum := 0
	for i := 0; i < len(arr); i++ {
		sum += arr[i]
	}
	return sum
}

// Create an alias for int32 as MyInt
type MyInt int32

func main() {
	// Without the Generic Approximation in
	// the Integers32 type interface,
	// this code wouldn't work since
	// alias type MyInt isn't an int32 OR a uint32 type
	nums := []MyInt{MyInt(1), MyInt(2), MyInt(3)}
	total := SumNums(nums)
}
```

## Generic Structures

In addition to `generic functions`, you can also have `generic structures`

```go
type MyStruct[T constraint] struct {
	// ...
}
```

```go
// This struct is generic
// the content property can be any type
type Box[T any] struct {
	content T
}

func (b *Box[T]) Set(content T) {
	b.content = content
}

func (b *Box[T]) Get() T {
	return b.content
}

func main() {
	// Create boxes of different types
	intBox := &Box[int]{content: 42}
	strBox := &Box[string]{content: "Hello, Generics!"}
}
```

```go
import (
	"fmt"
	"constraints"
)

// Define a generic type
// which can contain any string or number
// via the Ordered constraint
type MyArray[T constraints.Ordered] struct {
	inner []T
}

// Since this receiver function
// references the MyArray Generic,
// it uses type T
func (m *MyArray[T]) Max() T {
	max := m.inner[0]
	for _, value := range m.inner {
		if value > max {
			max = value
		}
	}
	return max
}

func main() {
	// Declare an integer MyArray Structure
	arrA := MyArray[int]{inner: []int{1, 2, 3, 4, 5}}

	// Declare an float MyArray Structure
	arrB := MyArray[float32]{inner: []float32{1.1, 20.2, 3.3}}

	// Declare a string MyArray Structure
	arrC := MyArray[string]{inner: []string{"c", "b", "a"}}

	fmt.Println(arrA.Max())
	fmt.Println(arrB.Max())
	fmt.Println(arrC.Max())
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [The Go Blog - An Introductin to Generics](https://go.dev/blog/intro-generics)
- [Golang Tutorial: Getting started with generics](https://go.dev/doc/tutorial/generics)
