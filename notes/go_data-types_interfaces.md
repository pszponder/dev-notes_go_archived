# Interfaces in Go

## Introduction to Interfaces in Go

Interfaces are a [reference type](go_data-types_value-vs-reference.md#reference-types) in Go. They are also a type of [collection](go_data-types_collection.md).

- Defines a set of method signatures (input / output) that a data type or struct can implement
  - Method signatures look like [function types](go_data-types_functions.md)
- Interfaces provide a way to achieve polymorphism and abstraction
- Interfaces are implemented _implicitly_
  - Any type which implements all of the methods (via [`receiver functions`](go_functions.md#receiver-functions-type-methods)) defined in an interface implicitly implements that interface
- Functions operating on interfaces should _never_ accept a pointer to an interface as a function parameter
  - You can still pass in either pointer or value types as an argument to a function invocation
- Better to use multiple smaller interfaces vs one very large interface (in terms of number of method signatures)

> Zero values for interface types (`interface{}`) is `nil`

## Defining an Interface

```go
type <Interface Name> interface {
	// method signatures
}
```

> NOTE: Conventionally, interfaces with one method should end with `er`

```go
// Define an interface called "Shape"
type Shape interface {
	Area() float65
	Perimeter() float64
}
```

## Implementing an Interface

> Any type which implements all of the methods (via [`receiver functions`](go_functions.md#receiver-functions-type-methods)) defined in an interface implicitly implements that interface

```go
// Define an interface called "Shape"
type Shape interface {
	Area() float65
	Perimeter() float64
}

// Define a type "Rectangle"
// Using receiver functions, can define methods
// for this type which match the "Shape" interface
// If this type has all methods as defined by the interface,
// then this type implicitly implements the Shape interface
type Rectangle struct {
	Width  float64
	Height float64
}

// Define the Area method for the Rectangle type
func (r Rectangle) Area() float64 {
	return r.Width * r.Height
}

// Define the Perimeter method for the Rectangle type
func (r Rectangle) Perimeter() float64 {
	return 2*r.Width + 2*r.Height
}

// We can define another type with methods
// to implement the Shape interface
type Circle struct {
	Radius  float64
}

// Define the Area method for the Circle type
func (c Circle) Area() float64 {
	return 3.14*c.Radius*c.Radius
}

// Define the Perimeter method for the Circle type
func (c Circle) Perimeter() float64 {
	return 2*3.14*c.Radius
}
```

## Using Interfaces

Once you have types that implement an interface, you can use those types through the interface. This allows you to write code that's polymorphic and can work with different types that satisfy the same interface.

> **CAUTION:** A function should always accept an interface parameter as a value type and _NOT_ a pointer type. When invoking the function, you can pass in either a pointer or value as the argument.

```go
// This function can accept any type
// which implements the Shape interface
func printAreaAndPerimeter(s Shape) {
	fmt.Printf("Area: %f\n", s.Area())
	fmt.Printf("Perimeter: %f\n", s.Perimeter())
}

func main() {
	rectangle := Rectangle{Width: 10, Height: 5}
	circle := Circle{Radius: 10}

	// We can pass in both rectangle and circle
	//  structs since they both implement
	//  the "Shape" interface
	printAreaAndPerimeter(rectangle)
	printAreaAndPerimeter(circle)
}
```

```go
// Define an interface
type Resetter interface {
	Reset()
}

// Define a "Player" type (struct)
type Player struct {
	health int
	energy int
}

// Define a "Reset" method on the "Player" struct
// "Player" now implements the "Resetter" interface
func (p *Player) Reset() {
	p.health = 100
	p.energy = 100
}

// Define a "Boss" type (struct)
type Boss struct {
	health int
	energy int
	inventory []string
}

// Define a "Reset" method on the "Boss" struct
// "Boss" now implements the "Resetter" interface
func (b *Boss) Reset() {
	b.health = 1000
	b.energy = 1000
	b.inventory = []string{"sword", "potion", "gem"}
}

// Define a function which accepts any type
// which implements the Resetter interface
func Reset(r Resetter) {
	r.Reset()
}

func main() {
	// Create instances of structs
	player := Player{10, 10}
	boss := Boss{123, 123, []string{"super rare item"}}

	// We can invoke "Reset" function on both structs
	Reset(&player)
	Reset(&boss)

	// Print the updated values
	fmt.Println("Player:", player)
	fmt.Println("Boss:", boss)
}
```

## Accessing Implementing Type

If you need to access the underlying type that implements an interface, can convert an interface back to its concrete type via [`type assertion`](go_data-types_asserting-types.md).

```go
// This function accepts any types which implement
// the Resetter interface
func ResetWithPenalty(r Resetter) {
	// Check if r is of type "Player"
	player, ok := r.(Player)
	if ok {
		player.health = 50
	} else {
		// Otherwise, implement the "Reset" method
		// defined by the Resetter interface
		r.Reset()
	}
}

// Alternative (using statement initialization in if statement)

func ResetWithPenalty(r Resetter) {
	// Check if r is of type "Player"
	// NOTE: Here we use statement initialization
	if player, ok := r.(Player); ok {
		player.health = 50
	} else {
		// Otherwise, implement the "Reset" method
		// defined by the Resetter interface
		r.Reset()
	}
}
```

## Interface Embedding / Composition

[Interface Embedding / Composition](go_data-types_embedding.md#embedded-interfaces)

## References / Resources

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [codecademy - interfaces](https://www.codecademy.com/resources/docs/go/interfaces)
- [NerdCademy - Learn Go Interfaces](https://www.youtube.com/watch?v=KB3ysH8cupY)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
- [Anthony GG - How to Refactor A Golang Project by Using Interfaces](https://www.youtube.com/watch?v=Ax7J_Ks0UWs)
- [Anthony GG - This Will Make Everyone Understand Golang Interfaces](https://www.youtube.com/watch?v=rH0bpx7I2Dk)
- [freeCodeCamp - Go Programming - Golang Course with Bonus Projects](https://www.youtube.com/watch?v=un6ZyFkqFKo)
