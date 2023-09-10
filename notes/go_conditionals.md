# Conditionals in Go

## if statement

```go
if <condition> {
  // code
}
```

## if-else statement

```go
if <condition> {
  // code
} else {
  // code
}
```

```go
playerRoll := 14
targetDifficulty := 15

if playerRoll >= targetDifficulty {
    fmt.Println("Success! You passed the skill check.")
} else {
    fmt.Println("Failure! You didn't meet the required difficulty.")
}
```

## else-if statement

```go
if <condition_1> {
  // code
} else if <condition_2> {
  // code
} else {
  // code
}
```

```go
alignment := "Neutral Good"

if alignment == "Lawful Good" {
    fmt.Println("You are a champion of justice and virtue.")
} else if alignment == "Neutral Good" {
    fmt.Println("You strive to do good, but your methods may vary.")
} else if alignment == "Chaotic Good" {
    fmt.Println("You follow your heart and stand up for the downtrodden.")
} else {
    fmt.Println("Your alignment is a mystery.")
}
```

## Statement Initialization

`Statement Initialization` in Go enables us to initialize a value and then immediately use it in a conditional statement.

```go
if <var> := <value>; <condition w/ var> {
  // code
} else if <condition2 w/ var> {
  // code
} else {
  // code
}
```

```go
// Initialize score and immediately use it
//  inside if statements

if score := getTestScore(); score >= 90 {
    // code for "A"
} else if score >= 80 && score < 90 {
    // code for "B"
} else if score >= 70 && score < 80 {
    // code for "C"
} else if score >= 60 && score < 70 {
    // code for "D"
} else {
    // code for "F"
}
```

## switch statement

### Basic Switch Statement

```go
switch <expression> {
  case <value_1>:
    // code
  case <value_2>:
    // code
  case <value_3>:
    // code
  .
  .
  .
  case <value_N> :
    // code
  default:
    // code
}
```

```go
characterClass := "Rogue"

switch characterClass {
case "Wizard":
    fmt.Println("You command the arcane forces of magic.")
case "Warrior":
    fmt.Println("You are a fearless and skilled warrior, mastering the art of combat.")
case "Rogue":
    fmt.Println("You are a cunning and agile rogue, adept at stealth and trickery.")
default:
    fmt.Println("You are a versatile adventurer of unknown class.")
}
```

### Case List

A case list enables you to evaluate code under a specific case for different values

```go
// multiple cases with the same code
switch <expression> {
  case <value_1>, <value_2>, ... <value_N>:
    // code
  .
  .
  .
  case <value_M> :
    // code
  default:
    // code
}
```

```go
switch x {
    case 1, 2, 3:
        // code for when x is 1, 2 or 3
    case 4, 5, 6:
        // code for when x is 4, 5 or 6
    default:
        // code
}
```

### Declaring variables in-line w/ switch

> **NOTE:** You can also declare the variable used inside the expression with the switch statement!

```go
// Notice how we declare and initialize variable
//  inline with the switch statement
// Make sure you end the in-line variable declaration with `;`
switch characterClass := "Warrior"; {
case "Wizard":
    fmt.Println("You command the arcane forces of magic.")
case "Warrior":
    fmt.Println("You are a fearless and skilled warrior, mastering the art of combat.")
case "Rogue":
    fmt.Println("You are a cunning and agile rogue, adept at stealth and trickery.")
default:
    fmt.Println("You are a versatile adventurer of unknown class.")
}
```

```go
// Can also define characterClass w/ a function
switch characterClass := getCharacterClass(); {
case "Wizard":
    fmt.Println("You command the arcane forces of magic.")
case "Warrior":
    fmt.Println("You are a fearless and skilled warrior, mastering the art of combat.")
case "Rogue":
    fmt.Println("You are a cunning and agile rogue, adept at stealth and trickery.")
default:
    fmt.Println("You are a versatile adventurer of unknown class.")
}
```

### Conditional Cases

You can use conditional expressions inside a switch statement as well

```go
switch <expression> {
  case <condition_1>:
    // code
  case <condition_2>:
    // code
  case <condition_3>:
    // code
  .
  .
  .
  case <condition_N> :
    // code
  default:
    // code
}
```

```go
switch result := sum(3, 5); {
    case result >= 10:
        // code for result >= 10
    case result > 5 && result < 10
        // code for 5 < result < 1o
    case result == 5:
        // code for result == 5
    case result >= 0 && result < 5:
        // code for 0 <= result < 5
    default:
        // code for default case
}
```

### Fallthrough

We can use the `fallthrough` keyword to execute the next case below the currently executed case

```go
switch y {
    case 1:
        // code
    case 2:
        // When this code executes,
        // it will also execute the code in case 3
        // code
        fallthrough
    case 3:
        // code
    default:
        // code
}
```

## Resources / References

- [codecademy - conditionals](https://www.codecademy.com/resources/docs/go/conditionals)
- [codecademy - switch](https://www.codecademy.com/resources/docs/go/switch)
- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [GitHub - ztm-golang](https://github.com/jayson-lennon/ztm-golang)
