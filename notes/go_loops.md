# Loops in Go

## Basic "for" Loop

```go
for initialization; condition; iteration {
    // Loop body
}
```

```go
for i := 0; i < 5; i++ {
    fmt.Println(i)
}
```

```go
for i := 10; i > 0; i-- {
  fmt.Println(i) // Counts down from 10 to 1
}
```

## "for" Loop as a "while" loop

> NOTE: You can use the `for` loop without initialization and iteration statements to create a `while` loop

```go
for condition {
    // Loop body
}
```

```go
sum := 0
for sum < 10 {
    sum += 2
}
```

## Infinite Loop

> NOTE: Create an infinite loop by omitting condition altogether

```go
for {
    // Loop body
}
```

```go
for {
    // Loop body

    // Use this check to break out of infinite loop
    if <condition> {
        break
    }
}
```

```go
count := 0
for {
    count++
    if count > 5 {
        break
    }
}
```

## Looping Over Iterables with Range

> NOTE: You can iterate over elements in slices, arrays, maps, strings, and channels using the `range` keyword

```go
for index, value := range iterable {
    // Loop body
}
```

```go
numbers := []int{1, 2, 3}
for idx, num := range numbers {
    fmt.Printf("Index: %d, Value: %d\n", idx, num)
}
```

```go
// Define a new array
arr := [...]string{"hello", "world", "!"}

// Loop through elements in an array
for idx, element := range arr {
    fmt.Println(idx, element)

    // Loop through the rune (characters) of each element
    // NOTE: _ means we don't wish to use idx in this case
    for _, ch := range element {
        fmt.Printf("  %q\n", ch)
    }
}
```

## Controlling Loops w/ "break" and "continue"

### Breaking out of current loop iteration

Use the `break` statement to exit the current loop prematurely.

```go
for i := 1; i <= 5; i++ {
    if i == 3 {
        break
    }
    fmt.Println(i)
}
```

### Continuing to next iteration

Use the `continue` statement to skip the rest of the current iteration and continue to the next iteration.

```go
for i := 1; i <= 5; i++ {
    if i == 3 {
        continue
    }
    fmt.Println(i)
}
```

## Looping Through Different Data Types

```go
// ARRAYS
numbers := [3]int{1, 2, 3}
for i := 0; i < len(numbers); i++ {
    fmt.Println(numbers[i])
}

for _, num := range numbers {
    fmt.Println(num)
}
```

```go
// SLICES
numbers := []int{1, 2, 3}
for i := 0; i < len(numbers); i++ {
    fmt.Println(numbers[i])
}

for _, num := range numbers {
    fmt.Println(num)
}
```

```go
// STRINGS
message := "Hello"
for i := 0; i < len(message); i++ {
    fmt.Println(message[i])
}

for _, char := range message {
    fmt.Println(char)
}
```

```go
// MAPS
ages := map[string]int{
    "Alice": 25,
    "Bob":   30,
}

for name, age := range ages {
    fmt.Printf("%s is %d years old\n", name, age)
}
```

```go
// CHANNELS
ch := make(chan int)
go func() {
    ch <- 1
    ch <- 2
    close(ch)
}()

for num := range ch {
    fmt.Println(num)
}
```

## Resources / References

- [codecademy - loops](https://www.codecademy.com/resources/docs/go/loops)
