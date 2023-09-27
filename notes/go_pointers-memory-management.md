# Pointers in Go

## Intro to Pointers in Go

A `pointer` is a variable which stores a memory address as its value

- The value of a pointer variable is a memory address
- To access and manipulate the actual value referenced by the pointer, you have to `dereference` the pointer

Function calls in Go are `pass by value`

- When passing in an argument to a function, a copy of that value is made
- When a function operates on the argument, the original argument is not changed
- When working with large data sets, this can result in significant increases to the memory used by the program

> Using `pointers` in Go enables us to `pass by reference` arguments to functions (so we end up manipulating original data structure).

[Kantan Coding - Pointers](https://youtu.be/v-ttLYKqaO8?feature=shared)

[JamieDev - Golang pointers explained, once and for all](https://www.youtube.com/watch?v=sTFJtxJXkaY)

### When to Use Pointers?

[Anthony GG - Go Pointers - When & How to Use Them Efficiently](https://www.youtube.com/watch?v=3WsEDZRif6U)

Recall that functions copy data passed into them. If you are passing large amounts of data, it is inefficient and resource intensive to do so.

Use Pointers when:

- Updating application state
- Working with large amounts of data to avoid copying the data in your functions.

## Creating Pointers

`*` (Dereferencing Operator)

- Use `*` on a type to indicate the type is a pointer for that type
- Use `*` on a pointer variable to dereference the pointer
  - Access the value that is stored at the memory address referenced by the pointer
- Use `*` to define a function parameter as a pointer

`&` (Address Of Operator)

- Use `&` to get the memory address of an existing variable
  - Optionally create / assign the memory address to a pointer variable
- Use `&` to pass a pointer as an argument to a function invocation which expects a pointer as an argument

```go
myValue := <value> // Declare a variable

// Declare pointer of <type>
var myPtr *<type>

// Assign memory address of myValue to the pointer
myPtr = &myValue

// Dereference the pointer to access the value
fmt.Println(*myPtr)
```

```go
// Shortcut to create and assign pointer to variable
myValue := <value> // Define variable
myPtr := &myValue // Get mem address of variable and assign to ptr
```

```go
// EXAMPLE 1: Creating a Pointer

value := "hello"

// Declare pointer variable
var valuePtr *string

// Create a pointer to the string value
// and assign it to the pointer variable
valuePtr = &value
```

```go
// EXAMPLE 2: Creating Pointer (Shortcut)

value := "hello"
valuePtr := &value
```

```go
// https://youtu.be/v-ttLYKqaO8?feature=shared

answer := 42
fmt.Println(answer) // 42

// Create a pointer to an int type
var ptr *int

// Print the memory address of the "answer" variable
fmt.Println(&answer) // 0xc00001e0d0

// Assign ptr to the memory address of answer
ptr = &answer

// Dereference the ptr variable to
// access the actual value it is pointing to
fmt.Println(*ptr) // 42

// Dereference pointer to change the value it points to
*ptr = 99
fmt.Println(answer) // 99
```

## Passing Pointers to Functions

Can pass pointers to function arguments to modify original value rather than working with a copy of the value

- Use `*` to define function parameter as a pointer
- Use `&` to pass a pointer as an argument to a function invocation

```go
// Define function which accepts a pointer to a variable of type int
func modifyValue(ptr *int) {
	// Dereference the pointer to update the actual variable's value
	*ptr = 100
}

x := 42 // Declare variable
modifyValue(&x) // Pass a pointer to x to modify its value
fmt.Println(x)  // Output: 100
```

### Functions and Pointers to Reference Types

> **NOTE:** When using pointers with [reference types](go_data-types_value-vs-reference.md#reference-types) (slices, maps, channels, pointer, interfaces, funcitons) as function parameters in Go, you typically don't need to use the `*` operator to dereference them inside the function.

- This is because these data structures are already reference types, which means that when you pass them as pointers to functions, you're already passing a reference to the original data structure
- Any modifications made to the data structure inside the function will affect the original data structure
- You still need to define any pointer parameters in the function definition using `*`

```go
// Any modifications to the slice `sl` within the func
// will be reflected in the original slice passed
// by the caller, without any need for
// explicit dereferencing
func modifySlice(sl []int) {
	sl[0] = 999
}
```

### Functions and Pointers to Struct Types

In Go, even though [structs](go_data-types_structs.md) are [value types](go_data-types_value-vs-reference.md#value-types), Go allows you to to use the dot notation to directly access its fields without explicit dereferencing.

- This is a syntactical convenience provided by Go
- Under the hood, the Go compiler automatically dereferences the pointer pointing to the struct for you
- **CAUTION** If you want to modify the value the pointer points to (like assigning a whole new struct to that pointer), hen you'll need to dereference the struct.

```go
package main

import "fmt"

// Define a new struct type called "Person"
// with two fields:
// "Name" of type string and "Age" of type int.
type Person struct {
	Name string
	Age  int
}

func main() {
	// Here, we're creating a new instance of the "Person" struct
	// and taking its address using the & operator.
	// This means 'p' is a pointer to a 'Person' struct.
	p := &Person{Name: "Alice", Age: 25}

	// Even though 'p' is a pointer,
	// we can directly access the fields of the struct it points to
	// without explicitly dereferencing the pointer.
	// This is a syntactical convenience in Go.
	// The Go compiler understands this
	// and behind the scenes it's equivalent to doing *p.Name
	fmt.Println(p.Name) // Alice
}
```

```go
type MyStruct struct {
	field1 string
	field2 int
}

// Define function which accepts a pointer to a variable of type MyStruct
func modifyValue(ptr *MyStruct) {
	// Since ptr is a pointer to a struct type
	// Don't need to use * to dereference the struct's fields
	// Go automatically dereferences ptr
	ptr.field1 = "hi"
	ptr.field2 += 2
}

// Declare struct variable
x := MyStruct{
  field1 "hello"
  field2 1
}

modifyValue(&x) // Pass a pointer to x to modify its value
fmt.Println(x)  // Output: 100
```

```go
type MyStruct struct {
	Field int
}

// Since we are changing where the pointer
// points to inside the function
// (we want the pointer to point to a different struct)
// we also need to dereference the pointer
// to access the underlying struct
func assignNewStruct(s *MyStruct) {
	*s = MyStruct{Field: 100}
}
```

## Nil Pointers

- A pointer can have a special value called `nil` which means it doesn't point to any memory address
- It is a good idea to check for `nil` pointers before dereferencing them to avoid runtime errors

```go
var ptr *int // Declare a pointer var which points to nil

// Check if the pointer points to `nil`
if ptr == nil {
	fmt.Println("Pointer is nil")
}
```

## Automatic Dereferencing of Method Calls w/ Pointer Receivers

Method calls with [pointer receivers](go_functions.md#receiver-functions-type-methods) are automatically dereferenced

- Allows you to invoke a method with a pointer receiver using a value of the type the pointer points to
- Go will automatically take the address of the value for you

```go
package main

import "fmt"

// Counter struct definition.
type Counter struct {
	value int // value field to hold the count.
}

// Increment method defined with a pointer receiver.
func (c *Counter) Increment() {
	c.value++ // Increments the value of the Counter.
}

func main() {
	c := Counter{} // Creating an instance c of type Counter (not a pointer).
	fmt.Println("Initial value:", c.value) // Prints the initial value of c (0)

	// Calling the Increment method on c.
	// Though c is not a pointer,
	// Go automatically takes the address for the method call.
	// This is equivalent to (&c).Increment().
	c.Increment()

	// Prints the value after incrementing.
	// The value is now 1 because the Increment method modifies the Counter in-place.
	fmt.Println("After incrementing:", c.value)

	// Output:
	// Initial value: 0
	// After incrementing: 1
}

```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Anthony GG - Everything You Need To Know About Pointers in Golang](https://www.youtube.com/watch?v=mqH21m0MsWk&list=PL0xRBLFXXsP7-0IVCmoo2FEWBrQzfH2l8&index=7)
- [Anthony GG - Go Pointers - When & How to Use Them Efficiently](https://www.youtube.com/watch?v=3WsEDZRif6U)
- [Kantan Coding - Learn pointers](https://youtu.be/v-ttLYKqaO8?feature=shared)
- [NerdCademy - Go Pointer Basics](https://www.youtube.com/watch?v=WUPG9612XhM)
- [Ben Davis - GoLang POINTERS, the Basics in Less Than 4 Minutes!](https://www.youtube.com/watch?v=wqyvS27MkR0)
- [JamieDev - Golang pointers explained, once and for all](https://www.youtube.com/watch?v=sTFJtxJXkaY)
