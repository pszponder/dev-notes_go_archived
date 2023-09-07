# Go Packages

In Go, everything is organized into `packages`.

## What is a Package in Go?

A `package` in Go is essentially a named collection of one or more related `.go` files.

Every `.go` file that you write should begin with a `package <name>` statement to indicate the name of the `package` which that file is a part of.

Functions, types, variables, and constants defined in one source file are visible to all other source files within the same `package`.

- So as long as the `.go` files have the same package name, they can share info between each other

A `Go` package can span multiple files within the same directory. All files in a directory should have the same package declaration.

> In Go, one package == one directory

- All `.go` files in the same package should be contained in the same directory
- Shouldn't ever have `.go` files with different package names in the same directory
- For all non-`main` packages, the directory name that the code lives in should be the same as the package name.

### Example of Packages in Go

```go
// main.go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	// Get a random number between 0 and 99 inclusive
	n := rand.Intn(100)

	// Print it out
	fmt.Printf("Your lucky number from main.go is %d!\n", n)

	// Use a function from another source file which sharers the same package ("main")
	fmt.Printf("Your lucky number from random.go is %d!\n", randomNumber())
}
```

```go
// random.go
package main

import (
	"math/rand"
)

func randomNumber() int {
	return rand.Intn(100)
}
```

```bash
# Run both Go files in the directory
go run *.go
```

## Package vs Module (as Pertaining to Go)

- A `package` is a collection of Go **_source files_** (`.go`) in the same directory.
- A [`module`](go_modules.md) is a collection of Go `packages` with built-in dependencies and versioning

## Declaring a file to be part of a Package

Use the `package` keyword followed by the package name at the top of a `.go` file to declare that the file belongs to a specified package.

Ex. Declare the `main.go` file to belong to the `main` package

> **NOTE:** It is a convention to name the package the same name as the directory holding your go file.

```go
// Declare the package name which this file belongs to
package main

import (
	// Import any dependent packages
)

func main() {
	// Logic for main program goes here
}
```

## The "main" package

In Go, `main` is a special package name which indicates that the package contains the code for an executable application (the package contains code that can be build into a binary and run).

Any package with the name `main` must also contain a `main()` function somewhere in the package which acts as the entry point for the program.

> The standard package name for a project's entry point file (`main.go`) is `main`

```go
// Declare the package name which this file belongs to
package main

// Your logic goes here...
```

## The init function

We can use the [`init`](go_functions.md#init-function) function to perform any package-level initialization before executing the `main()` function.

## Importing Packages

Use the `import` keyword to import packages to be used by the current `.go` file.

We can either import a single package or multiple packages.

> **NOTE:** When importing packages which are not in the same directory, your import path should start relative to your module path which is defined in [go.mod](go_modules.md#gomod)

```go
// Import a single package
import "packageName"
import "namespace/packageName"
```

```go
// Import multiple packages
import (
	"package1Name"
	"package2Name"
	"package3Name"
	"namespace/packageName"
	// ...
)
```

### Importing Everything

By default, whenever you import a package in Go, you have to refer to the name of the package in your code.

You can instead use a `.` to avoid referencing the name of the package in your code

```go
import (
  . "fmt"
)

func main() {
  // Since we imported everything from the "fmt" package,
  // don't need to reference fmt.Printf()
  // instead, just use Printf()
  Printf("Hello world!")
}
```

### Renaming / Aliasing Packages

```go
import (
  // Instead of referencing "packageName" in code,
  // you can reference the alias name instead
  <alias-name> "namespace/packageName"
)
```

### Importing Standard Library Packages

When importing packages from the standard library, you only need to use the package name.

```go
// Import the "fmt" packages from the Go standard library
import "fmt"
```

We can then use the package and its associated methods

```go
// Declare the package name which this file belongs to
package main

// Import the built-in fmt package
import "fmt"

// "main()" is the entry point of the program
func main() {
	// Use the "Print" method from the "fmt" package
	// to output text to the console
	fmt.Print("Hello World")
}
```

[Standard Library - Go Packages](https://pkg.go.dev/std)

### Importing Custom or 3rd party Packages

If you are using custom or 3rd party packages (anything that is not part of Go's standard library), you need to prefix the imported package name with the [`module path`](go_modules.md#module-path--project-path) and the `directory path`.

When you are importing packages that are part of the same `module` as your current `.go` file, the import statement should look like this:

```go
import <module path>/<path to the package relative to your go.mod file>
```

Example of Importing Custom / 3rd Party Packages:

```txt
github.com/user/path/to/module/
├── util/
│   ├── fun.go
│   └── strings.go
├── main.go
└── go.mod
```

```go
// main.go
package main

import (
	"fmt"
	"github.com/user/path/to/module/util"
)

func main() {
	greeting := util.GetGreeting()
	fmt.Println(greeting)

	original := "hello"
	modified := util.UppercaseFirst(original)
	fmt.Printf("Original: %s, Modified: %s\n", original, modified)
}
```

```go
// fun.go
package util

// GetGreeting returns a greeting message.
func GetGreeting() string {
	return "Hello from util package!"
}
```

```go
package util

import "strings"

// UppercaseFirst capitalizes the first letter of a string.
func UppercaseFirst(s string) string {
	if s == "" {
		return ""
	}
	return strings.ToUpper(string(s[0])) + s[1:]
}
```

- Your module path is:
  - `github.com/user/path/to/module`.
  - Consider this to be the root of your file import path
- Your file path (from the root of the project directory) is:
  - `util`
- Your import string for your `util` package is:
  - "`github.com/user/path/to/module/util`"
- You have two files inside the `util` directory (both of which have the package name `util`)
  - `fun.go`
  - `strings.go`
- When you wish to use exported functions from either of the files in the `utils` package:
  - Use `import "github.com/user/path/to/module/util"`
  - Then use the `util` package name and dot notation to access functions from either of those files.
    - Ex. `util.GetGreeting()` to access the `GetGreeting()` function defined in the `fun.go` file.
    - Ex. `util.UppercaseFirst()` to access the `UppercaseFirst()` function defined in the `strings.go` file.

### Downloading third-party packages

In order to use 3rd party packages, you have to download them using the `go get` command

```bash
go get github.com/<user>/<3rd-party-package>
```

## Exporting Variables / Functions from a Package

To export a function or variable from a package, simply make sure that the function / variable name is written in `PascalCase` where the 1st letter of each word is capitalized.

> Something in Go code is _exported_ if its name is written in `PascalCase`. Otherwise, it is _unexported_

- Unexported entities are `private` to the package in which they are declared in. They are only visible to code _in the same package_
- Exported entities in a package are `public` and are visible to any code that `imports` the package

```go
var fooBaz string // This is an unexported variable.
var FooBar string // This is an exported variable.

func fooBaz() {...} // This is an unexported function.
func FooBar() {...} // This is an exported function.

type fooBaz struct {...} // This is an unexported type.
type FooBar struct {...} // This is an exported type.
```

### Private Package Variables / Functions:

Make sure to use `camelCase` when naming a globally scoped function or variable which you wish to avoid access to from an outside package

**NOTE:** You can still access a globally scoped variable or function written in `camelCase` between files which share the same package name

## Organizing import statements

Here is a good technique to organize imports using 4 separate groups separated by an empty line:

```go
import (
  <standard library packages>

  <packages from teh current module>

  <3rd-party packages>

  <aliased packages>
)
```

```go
// Example
import (
    "fmt"
    "net/http"
    "os"
    "runtime/debug"
    "sync"

    "example.com/internal/logger"
    "example.com/internal/smtp"

    "github.com/go-playground/form/v4"
    "github.com/spf13/pflag"

    _ "github.com/mattn/go-sqlite3"
)
```

## Internal Packages

Go v1.4 introduced the concept of [Internal Packages](https://go.dev/doc/go1.4#internalpackages).

An `internal package` is a Go `package` which is only accessible by components which share a common ancestor directory tree.

- An `internal package` **cannot** be imported by an external Go project / module, or even an internal package if the two packages don't share a common ancestor directory tree.

Typically, a Go project will have one [`internal directory`](go_structure-project.md#internal) located in its root directory.

For example, a package `/a/b/c/internal/d/e/f` can only be imported by code in the directory tree rooted at `/a/b/c`. It cannot be imported by code in `/a/b/g` or in any other repository.

```txt
/a
└── b
    └── c
        ├── internal
        │   └── d
        │       └── e
        │           └── f
        └── g
```

In the diagram above, any packages listed under the `internal` directory are inaccessible from outside `/a/b/c` subtree

Example 2 of internal directory:

```txt
├── internal
    ├── config
    |   └── config.go
    └── stor
        └── store.go
```

The `internal/config` and `internal/store` directories contain the `config` and `store` packages respectively.

Each can be imported for example by `cmd/myapp/main.go` with:

```go
import (
	"github.com/user/repo/internal/config"
	"github.com/user/repo/internal/store"
)
```

But **cannot be imported by another Go project**, that is outside of `github.com/user/repo`.

### Creating an Internal Package

To create an `internal package`, simply store it in a special directory named `internal/` (either directly inside it or into a nested directory inside the `internal/` directory).

- `internal/` is a special directory name recognized by the `go` tool which will prevent one package from being imported by another unless both share a common ancestor directory tree.

### When to use Internal Packages?

Use `internal package`(s) to prevent packages from being imported outside of the containing Go Project / module

## Finding Packages to Use in Your Program

Refer to the [Go Standard Library](https://pkg.go.dev/std) for information about different built-in packages available to use in Go.

## Package Conventions

### Package Naming Conventions

- The name of the `package` should be the same as the name of the parent directory (except for the `main` package).
  - Ex. All `.go` files stored under the `util` directory should be part of the `util` package name.
- Use single, lowercase words for package names.
- Don't use generic names for package names
  - Avoid giving a package a name that is commonly used in client code or is very generic
  - Examples of **BAD** package names: `utils`, `helpers`, `buf`, `common`, `script`, etc.

[Go Package Naming Conventions](https://go.dev/blog/package-names)

### Package Nesting Conventions

**CAUTION:** Avoid nesting packages by default

- It is better to try to maintain a relatively flat file tree

When should I nest packages?

- You can nest packages if you have different implementations for the same interface (e.g. a store interface)
- You can nest packages if you start having a lot of Go files (more than 10) and it really makes sense to make sub-packages

### When to split your code into multiple packages?

It's a common mistake to overuse packages and create too many of them.

Reasons to split code into additional packages:

- You want a convenient way to reuse the code, or make it available for reuse
- You want to isolate / enforce some boundary between package code and rest of your code
- You have some complex code that acts as a "black box" and moving it to a standalone package will reduce cognitive overhead when working with the rest of your code

## Working with Multiple Packages

[Working with multiple packages](go_modules.md#working-with-multiple-packages)

## References / Resources

- [Techworld with Nana - Golang Tutorial for Beginners | Full Go Course](https://www.youtube.com/watch?v=yyUHQIec83I)
- [Official Docs - How to Write Go Code](https://go.dev/doc/code)
- [Standard library - Go Packages](https://pkg.go.dev/std)
- [NerdCademy - GoLang Packages and Modules | Beginners Go Tutorial](https://www.youtube.com/watch?v=nLaxs5w9bZc&list=PLujhHB_uAFJws6Vv5q1KDoaQ4YcpS9UOm&index=21&t=20s)
- [Use Internal packages to reduce your public API surface](https://dave.cheney.net/2019/10/06/use-internal-packages-to-reduce-your-public-api-surface)
- [Official Docs - Go - Internal Packages](https://go.dev/doc/go1.4#internalpackages)
- [Official Docs - Go - Package Names](https://go.dev/blog/package-names)
- [Alex Edwards - An introduction to Packages, Imports and Modules in Go](https://www.alexedwards.net/blog/an-introduction-to-packages-imports-and-modules)
