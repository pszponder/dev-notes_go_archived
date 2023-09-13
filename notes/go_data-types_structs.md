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

[`Receiver Functions`](go_functions.md#receiver-functions-type-methods)

## Resources / References

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [codecademy - Composition](https://www.codecademy.com/resources/docs/go/composition)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
