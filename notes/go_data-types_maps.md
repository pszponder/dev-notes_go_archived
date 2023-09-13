# Maps in Go

## Intro to Maps in Go

Maps are a [reference type](go_data-types_value-vs-reference.md#reference-types) in Go. They are also a type of [collection](go_data-types_collection.md) in Go

- A `map` is a collection of key-value pairs
- Similar to dictionaries or hash maps in other languages
- Provide an efficient way to retrieve values based on keys
- A map is created using the `make` function or using a composite literal

> Zero value for map types (`map[K]V`) is `nil`

## Creating Maps

```go
// Create a map using the `make` function
myMap := make(map[<keyType>]<valueType>)
```

```go
// Create a map using the composite literal
// Enables you to declare values in map
myMap := map[<keyType>]<valueType>{
    <key1>: <value1>
    <key2>: <value2>
    //...
    <keyN>: <valueN>
}
```

```go
// Create a map using the `make` function
myMap := make(map[string]int)
myMap["one"] = 1
myMap["two"] = 2

// Create a map using the composite literal
myMap2 := map[string]int{
    "three": 3,
    "four": 4,
}
```

## Accessing / Reading Values from a map

```go
// Create a new map
myMap := map[string]int{
    "one": 1,
    "two": 2,
    "three": 3,
    "four": 4,
}

// Access values from the map using keys
myMap["one"] // 1
myMap["two"] // 2
myMap["three"] // 3
myMap["four"] // 4

// Accessing non-defined keys returns default value
myMap["notAKey"] // 0 (default value for int type)
```

## Adding key-value pairs to a map

```go
// Create a new map
myMap := make(map[string]int)

// Add key-value pairs to the map
myMap["hello"] = 0
myMap["goodbye"] = 1
myMap["ten"] = 10
myMap["99"] = 99
```

## Removing key-value pairs from a map

```go
// Create a new map
myMap := map[string]int{
    "one": 1,
    "two": 2,
    "three": 3,
    "four": 4,
}

// Remove key-value pairs from the map
delete(myMap, "two")
delete(myMap, "three")
```

## Checking if key-value pair exists

When querying a map for a particular key, you can actually extract a 2nd value which is a boolean value indicating whether the key-value pair exists or not

```go
// If value for key exists in map, `exists` is true
// otherwise, `exists` is false
value, exists := myMap[<key>]
```

```go
// Create a new map
myMap := map[string]int{
    "one": 1,
    "two": 2,
    "three": 3,
}

// `exists` is boolean value which indicates
//  if the key-value pair was found in the map
value, exists := myMap["four"]

if exists {
    fmt.Printf("%s exists in the map with value %d\n", key, value)
} else {
    fmt.Printf("%s does not exist in the map\n", key)
}
```

## Iterating through key-value pairs in a map

[Iterate over a Map](go_loops.md#looping-over-iterables-with-range)

```go
for key, value := range myMap {
    //...
}
```

## Resources / References

- [codecademy - map type](https://www.codecademy.com/resources/docs/go/map)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
