# Structs in Go

## Introduction to Structs

Structs are a [value type](go_data-types_value-vs-reference.md#value-types) in Go. They are also considered to be a type of [collection](go_data-types_collection.md) in Go

- A `struct` is a composite type that groups together zero or more fields of various types
- Fields can have different data types and are accessed using dot notation
- Useful for creating custom data types
- Similar to a "class" in other programming languages

> Zero values for struct fields based on the field's respective zero values

## Defining Structs

> NOTE: Notice in the example below that the 1st struct and its properties are capitalized, this means it is exported. The 2nd struct is unexported since everything is lowercase.

```go
type <struct-name> struct {
	<field1> <type>
	<field2> <type>
	...
	<fieldN> <type>
}

// Note, can also define multiple fields at once
//  as long as they have the same type
type <struct-name> struct {
	<field1> <type>
	<field2> <field3> <field4> <type>
	...
	<fieldN> <type>
}
```

```go
// Define a struct (exported) named "Bicycle"
type Bicycle struct {
	Brand   string
	Model   string
	Color   string
	Gear    int
	HasBell bool
}

// Define an unexported struct named "bicycle"
type bicycle struct {
	brand   string
	model   string
	color   string
	gear    int
	hasBell bool
}

// Define another exported struct
type Pizza struct {
  Name        string
  Size        string
  Toppings    []string
  IsDelicious bool
}
```

```go
// Define a struct
type Person struct {
	FirstName string
	LastName  string
	Age       int
}

// Define a slice of Person structs
people := []Person{
	{"Alice", "Smith", 30},
	{"Bob", "Johnson", 25},
	{"Charlie", "Brown", 35},
}

// Define an array of Person structs
var people [3]Person
people[0] = Person{"Alice", "Smith", 30}
people[1] = Person{"Bob", "Johnson", 25}
people[2] = Person{"Charlie", "Brown", 35}
```

## Instantiating Structs

Structs are instantiated by:

- Referencing the name of the struct
- Passing zero, any or all of the fields in the struct
- Fields without a defined value are initialized w/ zero-values by default

```go
// Define a struct
type Person struct {
	FirstName string
	LastName  string
	Age       int
}

// Create a struct variable
person1 := Person{
	FistName: "Legolas"
	LastName: "Greenleaf",
	Age:      230,
}

// Access values from the struct variable
person1.FirstName // "Legolas"
person1.LastName  // "Greenleaf"
person1.Age       // 230

// NOTE: You can also instantiate a struct
//  without specifying the field name.
//  Make sure you follow the order of the fields
//  as they were defined in the struct in this case
person2 := Person{
	"Legolas"
	"Greenleaf",
	230,
}

// Instantiating fields inline
person3 := Person{"Legolas", "Greenleaf", 230}
```

## Anonymous Structs

Go allows you to define anonymous / inline structs on the fly

```go
// NOTE: All fields in this struct will contain default values
var myStruct struct {
	field1 string
	field2, field3 int
}

// Update default values in struct
myStruct.field1 = "hello"
myStruct.field2 = 1
myStruct.field3 = 2
```

```go
// Declare and initialize an anonymous struct via shorthand
person := struct {
	FirstName string
	LastName  string
	Age       int
}{
	FirstName: "Legolas",
	LastName:  "Greenleaf",
	Age:       230,
}

// NOTE: Don't have to pass in field names when specifying value
person2 := struct {
	FirstName string
	LastName  string
	Age       int
}{
	"Legolas",
	"Greenleaf",
	230,
}

// Access values from the struct variable
person.FirstName // "Legolas"
person.LastName  // "Greenleaf"
person.Age       // 230

person2.FirstName // "Legolas"
person2.LastName  // "Greenleaf"
person2.Age       // 230
```

```go
	// Declare a slice of anonymous structs
	people := []struct {
		FirstName string
		LastName  string
		Age       int
	}{
		{"Alice", "Smith", 30},
		{"Bob", "Johnson", 25},
		{"Charlie", "Brown", 35},
	}

	// Access and print values
	fmt.Println(people[0].FirstName) // Outputs: Alice
	fmt.Println(people[1].Age)       // Outputs: 25
```

## Struct Methods (Receiver Functions)

[Receiver Functions](go_functions.md#receiver-functions-type-methods)

## Empty Structs

An empty struct is a struct that has no fields and occupies zero bytes of memory.

```go
// Define an empty struct
type EmptyStruct struct{}

// Instantiate the empty struct
emptyStruct := EmptyStruct{}

// Sometimes, you will see an empty struct instantiation like this
//  (ex. signalling w/ channels):
// struct{} defines an empty struct type
// struct{}{} instantiates an empty struct
struct{}{}
```

### Uses for Empty Structs

1. **Signal Intent and NOT Store State** - Used to communicate that a type only needs methods, and doesn't need to store any state

```go
package main

import "fmt"

// Printer is an empty struct signaling intent. It doesn't store state.
type Printer struct{}

// PrintText method for the Printer type.
func (p Printer) PrintText(text string) {
    fmt.Println("Printing text:", text)
}

// PrintImage method for the Printer type.
// It just demonstrates the intent and doesn't actually print an image.
func (p Printer) PrintImage(imageName string) {
    fmt.Println("Printing image:", imageName)
}

// NewPrinter is a constructor function for our Printer.
// It ensures users understand they're working with an intended type.
func NewPrinter() Printer {
    return Printer{}
}

func main() {
    // Using the constructor to signal we're working with a printer.
    p := NewPrinter()

    p.PrintText("Hello, world!")
    p.PrintImage("landscape.jpg")
}
```

2. **Implement sets** - Go doesn't have a built-in `set` type. Can use a combination of [maps](go_data-types_maps.md) and empty structs to create a set. Why would you want to use a `set`?
   1. **Ensuring Uniqueness**: If you have a list of items and you want to make sure there are no duplicates, you can use a set. As you add items to the set, any duplicates will naturally be ignored because a map key (or set element in this context) can only appear once.
   2. **Efficient Membership Checks**: Checking if an element exists in a set (or a map's keys) is efficient (average-case O(1) time complexity). So, if you have frequent lookups to check the presence of items, a set is beneficial.
   3. **Set Operations**: If you're performing classic set operations like union, intersection, or difference, using a set makes these operations more straightforward.
   4. **Data Filtering**: If you have a list of items that you want to filter based on some criteria, using a set to store those criteria can make the filtering process faster and more intuitive.
   5. **Preventing Accidental Data Modification**: Using an empty struct (`struct{}{}`) as the map value in the set (as shown in the example) ensures that there's no meaningful value associated with the key. This ensures that users of the set won't accidentally modify some associated data since there is none.

```go
package main

import "fmt"

func main() {
	set := make(map[string]struct{})

	// Add elements to the set
	// (duplicates will NOT get added since map keys are unique)
	items := []string{"a", "b", "a", "c", "b", "d"}
	for _, item := range items {
		set[item] = struct{}{}
	}

	// Now, the set contains only unique items from the list
	fmt.Println("Unique items:")
	for item := range set {
		fmt.Println(item)
	}

	// Check if an element is in the set
	toCheck := []string{"a", "e", "d"}
	for _, item := range toCheck {
		if _, ok := set[item]; ok {
			fmt.Printf("%s is in the set\n", item)
		} else {
			fmt.Printf("%s is not in the set\n", item)
		}
	}
}
```

3. **Channels for Signaling** - When using [channels](go_concurrency_channels.md) for signaling purposes (i.e., you care about the signal, not the data being sent), you can use a channel of empty structs. This makes it clear that the value being sent or received isn't important.

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// Create a new channel of empty struct.
	// This channel will be used for signaling.
	done := make(chan struct{})

	// Start a new goroutine.
	// Goroutines are lightweight threads managed by the Go runtime.
	go func() {
		// Simulate some work by printing a message.
		fmt.Println("Doing some work...")

		// Pause the execution for 2 seconds to mimic some processing time.
		time.Sleep(2 * time.Second)

		// Send an empty struct to the 'done' channel,
		// signaling that the work is complete.
		done <- struct{}{}
	}()

	// Wait for a value to be received from the 'done' channel.
	// This blocks the main goroutine until the anonymous goroutine
	// sends a value to the 'done' channel.
	<-done

	// Print a message indicating that the goroutine has completed its work.
	fmt.Println("Work is done!")
}
```

4. **Space Efficiency in Data Structures** - If creating a complex data structure where some nodes / elements may not need to store data (e.g., tree nodes in some algorithms, or placeholders), use empty structs to save memory.

```go
type TreeNode struct {
	Left  *TreeNode
	Right *TreeNode
	Value interface{} // don't need to hold value for this implementation
}

type PlaceholderNode struct {
	Left  *PlaceholderNode
	Right *PlaceholderNode
}
```

5. **Type-safe way to implement feature toggles** - Empty structs can be used to define whether a feature is enabled or not, giving you a compile-time check.

```go
/*
How does this work?
1. Type Safety: Each feature is represented by a distinct type,
   ensuring that you can't mistakenly pass one feature flag
   into the function of another. For instance, you can't
   accidentally pass `CacheEnabled` into `UseDatabase`
2. Clarity and Self-Documentation: This code clearly communicates
   that specific types are expected for each function, making it
   evident when you read the code what's expected and what each
   part does.
3. Scalability: As you introduce new features, you can simply
   create a new empty struct for it and a new function
   (or modify existing functions) to accept the new feature flag,
   ensuring consistency nd type safety across your codebase.
*/

package main

import "fmt"

// Feature flags as empty structs
type DatabaseFeature struct{}
type CacheFeature struct{}
type LogFeature struct{}

// Feature instances indicating they are enabled
var DatabaseEnabled = DatabaseFeature{}
var CacheEnabled = CacheFeature{}
var LogEnabled = LogFeature{}

// Function to use the database feature
func UseDatabase(f DatabaseFeature) {
	fmt.Println("Using the new database backend!")
}

// Function to use the cache feature
func UseCache(f CacheFeature) {
	fmt.Println("Using the new caching mechanism!")
}

// Function to use the logging feature
func UseLog(f LogFeature) {
	fmt.Println("Using the enhanced logging!")
}

func main() {
	// Simulating the enabling of different features
	UseDatabase(DatabaseEnabled)
	UseCache(CacheEnabled)
	UseLog(LogEnabled)
}
```

## Generic Structs

[Generic Structs](go_generics.md#generic-structures)

## Struct Embedding / Composition

[Struct Embedding / Composition](go_data-types_embedding.md#embedded-structs)

## Resources / References

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [codecademy - Composition](https://www.codecademy.com/resources/docs/go/composition)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
