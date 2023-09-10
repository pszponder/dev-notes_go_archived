# Pointers in Go

## Intro to Pointers in Go

Function calls in Go are `pass by value`

- When passing in an argument to a function, a copy of that value is made
- When a function operates on the argument, the original argument is not changed
- When working with large data sets, this can result in significant increases to the memory used by the program

> Using `pointers` in Go enables us to `pass by reference` arguments to functions (so we end up manipulating original data structure).

Pointers are variables that point to locations in memory

- The value of a pointer variable is a memory address
- To access and manipulate the actual value referenced by the pointer, you have to `dereference` the pointer

### When to Use Pointers?

Recall that functions copy data passed into them. If you are passing large amounts of data, it is inefficient and resource intensive to do so.

Use pointers when you are working with large amounts of data to avoid copying the data in your functions.

## Creating Pointers

`*` (Dereferencing Operator)

- Use `*` on a type to indicate the type is a pointer
- Use `*` on a pointer variable to dereference the pointer (access the value that is stored at the memory address referenced by the pointer)
- Use `*` to define a function parameter as a pointer

`&` (Address Operator)

- Use `&` to get the memory address of an existing variable (and optionally create / assign the memory address to a pointer variable)
- Use `&` to pass a pointer as an argument to a function invocation

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
// EXAMPLE 1: Creating Pointer

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

### Functions and Pointers to Collection Types

> **NOTE:** When using pointers with [collection types](go_data-types_collections.md) (arrays, slices, maps, or structs, etc.) as function parameters in Go, you typically don't need to use the `*` operator to dereference them inside the function.

- This is because these data structures are already reference types, which means that when you pass them as pointers to functions, you're already passing a reference to the original data structure
- Any modifications made to the data structure inside the function will affect the original data structure
- You still need to define any pointer parameters in the function definition using `*`

```go
type MyStruct struct {
    field1 string
    field2 int
}

// Define function which accepts a pointer to a variable of type MyStruct
func modifyValue(ptr *MyStruct) {
    // Since ptr is a pointer to a struct type
    // Don't need to use * to dereference the struct's fields
    // Go automatically dereferences ptr when refer
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

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Anthony GG - Everything You Need To Know About Pointers in Golang](https://www.youtube.com/watch?v=mqH21m0MsWk&list=PL0xRBLFXXsP7-0IVCmoo2FEWBrQzfH2l8&index=7)
