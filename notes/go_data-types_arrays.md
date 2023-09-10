# Arrays in Go

## Arrays Overview

> Arrays are a type of [collection](go_data-types_collection.md) in Go

- An `array` is a fixed-size sequence of elements of the same type
- It is defined using square brackets and specifying the size
- Elements of an array are indexed starting from 0
- The size of an array cannot be changed after being created
- Arrays are passed to a function as copies of the array (changing the array inside the function does not change the original input array)

> Zero value for an array is based on the array's type zero value (ex. array if int is initialized to an array of 0 if no value is defined)

## Creating Arrays

```go
// NOTE: n represents length of array
// NOTE: type represents the data types held by the array

// Using var keyword to initialize empty array
var myArr [n]type

// Short assignment statement to initialize + populate the array
myArr := [n]type{el1, el2, ..., eln}

// Use "..." to avoid specifying # of elements in array
myArr := [...]type{el1, el2, ..., eln}
```

```go
// Initialize an empty array
var arr1 [5]int

// Populate the empty array with values
arr1[0] = 10
arr1[1] = 20
arr1[2] = 30
arr1[3] = 40
arr1[4] = 50
```

```go
// Create an array and populate it with values
numbers2 := [5]int{10, 20, 30, 40, 50}
```

```go
// Create array w/ values but don't specify
// number of elements in the array
// Go will know how many values in the array
numbers3 := [...]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

### Creating Multidimensional Arrays

```go
var arr [x1][x2]...[xn]type

arr := [x1][x2]...[xn]type

// Declare & Initialize w/ values
arr := [x1][x2]...[xn]type{
    [x1][x2]...[xn]type{el1, el2, ...elN},
    [x1][x2]...[xn]type{el1, el2, ...elN},
    //...
    [x1][x2]...[xn]type{el1, el2, ...elN},
}

// NOTE: type declaration is optional
arr := [x1][x2]...[xn]type{
    {el1, el2, ...elN},
    {el1, el2, ...elN},
    //...
    {el1, el2, ...elN},
}

// NOTE: Use ellipsis to define each subarray size
// NOTE2: Go requires at least 1 dimension's size to be specified
arr := [...][...]...[xn]type{
    {el1, el2, ...elN},
    {el1, el2, ...elN},
    //...
    {el1, el2, ...elN},
}
```

```go
// 2D array w/ 3 rows and 3 columns
multiArray := [3][3]int{
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9},
}
```

```go
// 2D tic-tac-toe board
board := [3][3]string{
    [...]string{"_", "_", "_"},
    [...]string{"_", "_", "_"},
    [...]string{"_", "_", "_"},
}

board[0][0] = "X"
board[1][0] = "X"
board[2][0] = "Y"
//...

// NOTE: type declaration of string array is optional
board2 := [3][3]string{
    {"_", "_", "_"},
    {"_", "_", "_"},
    {"_", "_", "_"},
}

board2[2][2] = "Y"
board2[1][0] = "X"
board2[2][3] = "X"
//...

// Can use ellipsis to let Go figure out array size
// Specify at least 1 dimension
board3 := [...][3]string{
    {"_", "_", "_"},
    {"_", "_", "_"},
    {"_", "_", "_"},
}

board2[2][2] = "Y"
board2[1][0] = "X"
board2[2][3] = "X"
//...
```

## Getting length of an array

Use the `len()` method to get the length of an array

```go
// Create an array
myArr := [...]int{1, 2, 3}

// Get the length of the array
len(myArr) // 3
```

## Accessing values from Arrays

```go
// Define the array
arr := [3]string{"hello", "world", "!"}

// Access values from the array via index
arr[0] // "hello"
arr[1] // "world"
arr[2] // "!"
arr[len(arr) - 1] // "!"
arr[10] // CAUTION: This will result in a runtime panic
```

### Looping through values in an array

Use the `len` function to [loop](go_loops.md) through elements in an array

```go
arr := [...]int{2, 3, 4, 5, 6, 7}

for i := 0; i < len(arr); i++ {
    element := arr[i]
    fmt.Println(arr)
}
```

## Change values in an array

```go
// Create a new array
arr := [...]int{1, 2, 3, 4, 5}

// Update values in the array
arr[0] = 10 // [10 2 3 4 5]
arr[1] = 20 // [10 20 3 4 5]
arr[2] = 30 // [10 20 30 4 5]
arr[3] = 30 // [10 20 30 40 5]
arr[4] = 50 // [10 20 30 40 50]
```

## Slicing Arrays / Slices

> NOTE: You can also slice slices

```go
// Create a new array
arr := [...]int{1, 2, 3, 4, 5}

// Slice from start to endIdx
arrStart := arr[:3] // [1 2 3]

// Slice from startIdx to end
arrEnd := arr[3:] // [4 5]

// Slice from startIdx to endIdx (exclusive)
startIdx := 1
endIdx := 3
arrSlice := arr[startIdx:endIdx] // [2, 3]
```

## Create a copy of an array

```go
// Create an array
originalArray := [...]int{10, 20, 30, 40, 50}

// Create new variable and assign a copy of the original array
copiedArray := originalArray
```

## Resources / References

- [codecademy - Arrays](https://www.codecademy.com/resources/docs/go/arrays)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
