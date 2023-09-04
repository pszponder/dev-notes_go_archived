# Slices in Go

## Intro to Slices in Go

> Slices are a type of [collection](go_data-types_collection.md) in Go

- A `slice` is a dynamic array that can grow or shrink in size
- Under the hood, a slice references an array.
- A slice is defined with empty square brackets
- As with an array, a slice can contain only one type of data
- Slices can be resized after their initial creation
- Slices are passed into functions as references to the original array (so if the function changes the slice, the original referenced array will also be affected)
- Commonly used for working with sequences of data

> Zero value for Slice types (`[]T`) is `nil`

### Slices Under the Hood

> Slices are dynamic views into arrays

Components of a Slice:

- Pointer: Points to the 1st element of the slice within the underlying array
- Length: Number of elements in the slice
- Capacity: Maximum number of elements that the slice can contain without reallocating memory

> When you create a new slice using a composite literal
> (ex. `mySlice := []int{1, 2, 3}`),
> under the hood, Go creates an anonymous array with the same elements defined during the slice creation.

## Creating a new Slice

```go
// NOTE: type represents the data types held by the slice

// Using var keyword to initialize empty slice
var mySlice []type

// Short assignment statement to initialize + populate the slice
myArr := []type{el1, el2, ..., eln}
```

```go
// Defining an empty slice
var emptySlice []int

// Populate the empty slice using the append function
// Here, you are concatenating values 10, 20 and 30
//  to the emptySlice and overwriting value of emptySlice
emptySlice = append(emptySlice, 10, 20, 30) // [10 20 30]
```

```go
// Instantiate a slice with values
numbers := []int{10, 20, 30, 40, 50} // [10 20 30 40 50]
```

### Creating a slice from an array

```go
// startIdx is INCLUSIVE
// endIdx is EXCLUSIVE
<array-name>[startIdx:endIdx]
```

```go
// Create an array
myArray := [...]int{10, 20, 30, 40, 50}

// Create a slice with all values from array
mySlice := myArray[:]

// Create a new slice from the array given startIdx and endIdx
startIdx := 1
endIdx := 4
subslice := myArray[startIdx:endIdx] // [20 30 40]
```

### Creating Multidimensional Slices

```go
var multiSlice [][]...[]type

multiSlice := [][]...[]type

// Declare & Initialize w/ values
multiSlice := [][]...[]type{
    [][]...[]type{el1, el2, ...elN},
    [][]...[]type{el1, el2, ...elN},
    //...
    [][]...[]type{el1, el2, ...elN},
}

// NOTE: type declaration is optional
multiSlice := [][]...[]type{
    {el1, el2, ...elN},
    {el1, el2, ...elN},
    // ...
    {el1, el2, ...elN},
}
```

```go
// 2D slice with 3 rows and 3 columns
multiSlice := [][]int{
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9},
}
```

```go
// 2D tic-tac-toe board
board := [][]string{
    []string{"_", "_", "_"},
    []string{"_", "_", "_"},
    []string{"_", "_", "_"},
}

board[0][0] = "X"
board[1][0] = "X"
board[2][0] = "Y"
//...

// NOTE: type declaration of string array is optional
board2 := [][]string{
    {"_", "_", "_"},
    {"_", "_", "_"},
    {"_", "_", "_"},
}

board2[2][2] = "Y"
board2[1][0] = "X"
board2[2][3] = "X"
//...
```

## Getting length of a slice

```go
// Create a slice
mySlice := []int{1, 2, 3}

len(mySlice) // 3
```

## Accessing values from a slice

```go
// Create a slice
mySlice := []int{1, 2, 3}

// Access values from a slice
mySlice[0] // 1
mySlice[1] // 2
mySlice[2] // 3
mySlice[len(mySlice) - 1] // 3
mySlice[10] // CAUTION: This will result in a runtime panic
```

## Adding values to a slice

Use the `append` function to add an item to the end of the slice

```go
// Create a slice
numbers := []int{1, 2, 3}

// Add a value to a slice using the append function
// (and replace original value of numbers with the new slice)
numbers = append(mySlice, 40, 50, 60) // [1 2 3 40 50 60]
```

```go
// Create 2 slices
numbersA := []int{1, 2, 3}
numbersB := []int{4, 5, 6}

// Use `...` to unpack 2nd slice into individual elements
// NOTE: This is also called concatenation
numbersTotal := append(numbersA, numbersB...)
```

## Slicing Slices / Arrays

> NOTE: You can also slice arrays

```go
// Define a new slice
originalSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// Generate a slice of the slice
slicedSlice := originalSlice[2:6] // [3 4 5 6]

// Generate new slice from startIdx = 0 up to endIdx (exclusive)
sliceStart := originalSlice[:4] // [1 2 3 4]

// Generate new slice from startIdx to end of slice
sliceEnd := originalSlice[5:] // [6 7 8 9 10]
```

## Concatenating Slices

Use the `append` method to append slices together

```go
// Define a new slice
originalSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// Generate new slice from startIdx = 0 up to endIdx (exclusive)
sliceStart := originalSlice[:4] // [1 2 3 4]

// Generate new slice from startIdx to end of slice
sliceEnd := originalSlice[5:] // [6 7 8 9 10]

// Combine / concatenate slices together
// ... unpacks sliceEnd into individual values
newSlice := append(sliceStart, sliceEnd...)
// [1 2 3 4 5 6 7 8 9 10]

// Concatenate 3+ slices together to create a new slice
slice1 =: []int{1, 2, 3}
slice2 =: []int{4, 5, 6}
slice3 =: [int]{7, 8, 9}

concatenatedSlice := append(slice1, slice2...)
concatenatedSlice = append(concatenatedSlice, slice3...)
```

## Removing Values from Slices

```go
// Removing from Slices

// Define a new slice
originalSlice := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

// Removing last value of a slice
originalSlice = originalSlice[:len(originalSlice)-1]
// [1 2 3 4 5 6 7 8 9]

// Removing value from a slice at specified index
indexToRemove := 2
originalSlice = append(originalSlice[:indexToRemove], originalSlice[indexToRemove+1:]...)
// [1 2 4 5 6 7 8 9]
```

## Preallocating slices

Can preallocate a slice to hold a specific number of values using the `make()` function

- Useful when number of elements is known but values of elements are unknown

```go
preallocatedSlice := make([]<type>, <num-elements>)
```

```go
// Preallocate a slice to hold 5 integer elements
preallocatedSlice := make([]int, 5)
```

## Copying a slice

### Shallow Copy

A `Shallow Copy` references the original underlying array (so both the original and copied slice reference the same underlying array)

```go
originalSlice := []int{10, 20, 30, 40, 50}
copiedSlice := originalSlice[:]
```

### Deep Copy

A `Deep Copy` is a completely independent copy (it does not reference the original underlying array)

- Use the `copy`

```go
originalSlice := []int{10, 20, 30, 40, 50}

// Use `make` to preallocate a new destination slice
// with same length as the original slice
copiedSlice := make([]int, len(originalSlice))

// Copy elements from originalSlice into copiedSlice
copy(copiedSlice, originalSlice)

copiedSlice[2] = 99

fmt.Println(originalSlice) // [10 20 30 40 50]
fmt.Println(copiedSlice)   // [10 20 99 40 50]
```

## Resources / References

- [codecademy - Go Data Types](https://www.codecademy.com/resources/docs/go/data-types)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
