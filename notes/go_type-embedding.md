# Type Embedding

## Type Embedding

`Type Embedding` is the process of:

- Nesting [`structs`](go_data-types_structs.md) within other structs
- Nesting [`interfaces`](go_data-types_interfaces.md) within other interfaces
- Somewhat analogous to inheritance in object-oriented languages

## Embedded Interfaces

`Embedded Interfaces` are [`interfaces`](go_data-types_interfaces.md) embedded into other interfaces

- Implementing the interface requires all embedded functions to be implemented
- Reduces the need to write duplicate interface declarations
- Changes in embedded interfaces automatically propagate
  - Easier to maintain codebase (compiler errors will indicate where updates should be made)

> **CAUTION**: You cannot "override" methods in interfaces. If two embedded interfaces declare a method with the same name and signature, it's still considered as one method in the embedding interface. However, if two embedded interfaces declare method with the same name but different signatures, it will result in a compile-time error.

```go
package main

type InterfaceA interface {
    MethodA() string
}

type InterfaceB interface {
    InterfaceA      // Embedding InterfaceA into InterfaceB
    MethodB() string
}

// A type that implements InterfaceB must implement
// both MethodA() and MethodB()
type ConcreteType struct{}

func (c ConcreteType) MethodA() string {
    return "MethodA of InterfaceA"
}

func (c ConcreteType) MethodB() string {
    return "MethodB of InterfaceB"
}

func main() {
    var obj InterfaceB = ConcreteType{}
    fmt.Println(obj.MethodA())  // Output: MethodA of InterfaceA
    fmt.Println(obj.MethodB())  // Output: MethodB of InterfaceB
}
```

```go
type Whisperer interface {
	Whisper() string
}

type Yeller interface {
	Yell() string
}

// Embed Whisperer and Yeller Interfaces
//  into the Talker interface
// So the Talker interface must implement everything
//  from the Whisperer and Yeller interfaces
type Talker interface {
	Whisperer
	Yeller
}

func talk(t Talker) {
// Since a Talker type implements both Yell & Whisper methods,
// we can invoke both methods in this function
	fmt.Println(t.Yell())
	fmt.Println(t.Whisper())
}
```

## Embedded Structs

`Embedded Structs` are [`structs`](go_data-types_structs.md) embedded into other structs

The struct will have access to all [`receiver functions`](go_functions.md#receiver-functions-type-methods) (methods) and data of the embedded struct at the top level

- This is called field & method promotion
- Allows easy access of embedded struct data, without additional indirection / dereferencing

```go
package main

import "fmt"

type TypeA struct {
	FieldA int
}

// Declare method for TypeA
func (a TypeA) AMethod() string {
	return "This is a method from TypeA"
}

// Declare another method for TypeA
func (a TypeA) AMethod2() string {
	return "This is another method from TypeA"
}

// TypeB has access to all fields/methods of TypeA
//  due to type embedding of the TypeA struct
type TypeB struct {
	TypeA     // Embedding TypeA into TypeB
	FieldB int
}

// Overwrite the embedded method for TypeA
// with a custom implementation for TypeB
func (a TypeB) AMethod2() string {
	return "This method overwrites AMethod2 from TypeA"
}

func main() {
	b := TypeB{
		TypeA:  TypeA{FieldA: 10},
		FieldB: 20,
	}

	fmt.Println(b.FieldA)    // 10
	fmt.Println(b.FieldB)    // 20
	fmt.Println(b.AMethod()) // This is a method from TypeA
	fmt.Println(b.AMethod2()) // This method overwrites AMethod2 from TypeA
}
```

```go
type Account struct {
	accountId int
	balance int
	name string
}

// Embed the Account struct into the ManagerAccount struct
// ManagerAccount has all fields / methods
//  associated with Account
type ManagerAccount struct {
	Account
}

// Define method for Account Struct
func (a *Account) GetBalance() int {
	return a.balance
}

// Define another method for Account Struct
func (A Account) String() string {
	return fmt.Sprintf("Standard (%v) $%v \"%v\"",
		a.accountId,
		a.balance,
		a.name)
}

// Define a method for ManagerAccount
func (m ManagerAccount) String() string {
	return fmt.Sprintf("Manager (%v) $%v \"%v\"",
		a.accountId,
		a.balance,
		a.name)
}

func main() {
	mgrAcct := ManagerAccount{Account{2, 30, "Sue"}}

	// Since ManagerAccount struct
	// contains the embedded Account struct
	// any fields & methods defined for the Account struct
	// are "inherited" by the ManagerAccount struct
	fmt.Printf("%v\n", mgrAcct)
	fmt.Printf("%v\n", mgrAcct.GetBalance())
	fmt.Printf("%v\n", mgrAcct.accountId)
}
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Anthony GG - The Power of Struct Embedding And Interfaces in Golang](https://www.youtube.com/watch?v=fXZJu_JuH0A)
