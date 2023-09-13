# Value vs Reference Types in Go

## Basic Types

[Basic types](go_data-types_basic.md) are the fundamental types provided by the Go Language and include:

- [Numeric types](go_data-types_numbers.md) (`int`, `float64`, etc.)
- [Boolean type](go_data-types_booleans.md) (`bool`)
- [String type](go_data-types_strings-runes.md) (`string`)

## Value Types

`Value Type` refers to types whose instances are typically copied when they're assigned or passed into functions as arguments.

- When you assign one variable of a `value type` to another, a copy is made.
- When you pass a `value type` to a [function](go_functions.md), it receives a copy.
  - Modifications inside the function won't affect the original value outside of the function

`Value Types` in Go:

- [Basic Types](go_data-types_basic.md)
  - Numeric Types
  - Boolean Types
  - String Types
- [Arrays](go_data-types_arrays.md)
- [Structs](go_data-types_structs.md)

## Reference Types

`Reference Types` contain references to underlying data

- When you assign a variable of a reference type to another, both variables refer to the same underlying data
- When you pass a reference type to a [function](go_functions.md), it gets a reference to the same data
  - Changes inside the function will effect the original data outside of the function

`Reference Types in Go`:

- [Slices](go_data-types_slices.md)
- [Maps](go_data-types_maps.md)
- [Channels](go_data-types_channels.md)
- [Pointers](go_data-types_pointers.md)
- [Interfaces](go_data-types_interfaces.md)
- [Functions](go_data-types_functions.md) (when treated as values or passed around)

### Slices are Reference Types w/ a Twist

- A slice consists of a pointer to an array, length and capacity
- When you pass a slice to a function, the slice header (containing the pointer, length and capacity) is copied, but not the underlying array
- If you modify the elements of the slice inside the function, you'll be modifying the original data
- _HOWEVER_, if you change the slice itself inside the function, like by appending beyond its capacity (which leads to a new underlying array), this won't affect the original slice outside the function

## Resources / References
