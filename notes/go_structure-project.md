# Go Project Structure

## Initialize Project as a Module

The 1st thing you should always to when creating a new Go Project is to initialize the project as a [Go Module](./go_modules.md#initializing-a-go-module)

## Typical Project File Structure

Below is an example of a typical file structure for a Go Project / Module:

```txt
.
├── .git
├── cmd
|   ├── myapp
|   |   └── main.go
|   └── myotherapp
|       └── main.go
├── examples
|   └── example1
|       └── main.go
├── internal
|   ├── config
|   |   └── config.go
|   └── store
|       └── store.go
├── pkg
|   ├── public1
|   |   └── public1.go
|   └── public2
|       └── public2.go
├── go.mod
└── go.sum
```

## Root Directory

```txt
.
├── .git
├── cmd
├── examples
├── internal
├── pkg
├── go.mod
└── go.sum
```

The root directory of a good Go project contains the following files/sub-directories:

- `.git` directory (stores all `git` related information)
- `go.mod` (contains project name / path, Golang version, project deps)
- `go.sum` (contains checksums for project go dependencies)
- `cmd` (contains sub-directories for the entry point of each project application / program)
- `internal` (contains `internal package`s only accessible to the current Go Project / Module)
- `pkg` (contains `package`s which can be shared / imported by outside Go Projects / Modules)
- `examples` (contains runnable example code to demonstrate your API's or other code found in `pkg`)

### .git directory

The `.git` directory is automatically created when initializing a `git` repository in the root directory of the project using the `git init` command.

### go.mod file

[`go.mod`](go_modules.md#gomod)

### go.sum file

[`go.sum`](go_modules.md#gosum)

### cmd

A Go project may contain multiple programs / applications.

Each program requires an `entry point`, which is accomplished through the use of a [`main.go`](go_project-setup.md#create-the-project-entry-point) file in Go.

The `cmd` directory contains the main application entry point files for the project, with the directory name matching the name for the binary.

- The `cmd` directory contains sub-directories for each program / application contained as part of the project / module.
- Each of these app sub-directories contain a single `main.go` file.

Example of `cmd` directory:

```txt
├── cmd
    ├── myapp
    |   └── main.go
    └── myotherapp
        └── main.go
```

Each `main.go` file represents the `package main` and contains only the `main()` function.

The code within each `main.go` file should be minimal as most of the logic of the application / program should be abstracted away into packages found within the `internal` directory.

### internal

The `internal` directory contains all of the project's [`internal packages`](go_packages.md#internal-packages).

### pkg

The `pkg` directory is indented to contain packages which can be shared to and imported by other projects.

Example of things found in the `pkg` directory: API clients, or utility functions which may be handy for other projects but don't justify their own projects

In our example, we have:

```txt
├── pkg
    ├── public1
    |   └── public1.go
    └── public2
        └── public2.go
```

We have two packages, `public1` and `public2`. Each can be imported by any Go project (including this one) with for example

```go
import (
	"github.com/user/repo/pkg/public1"
)
```

**CAUTION:** You should really limit the packages placed inside the `pkg` directory.

It's good practice to have all packages in the `internal` directory and only move them to the `pkg` directory once they have been proven to:

1. Be stable, ideally with full (and _deep_) unit testing coverage
2. Have their exported Go API stable

### internal vs pkg directories

An `internal` directory contains `internal packages` which are meant to only be used by components within the containing Go Project / Module

A `pkg` directory contains packages which are available to import by other Go projects (ex. external APIs)

### examples

The `examples` directory should contain runnable examples to showcase your publicly exported Go API from `pkg`.

Each example should have a descriptive directory name and a runnable `main.go` file with only the `func main()` function.

For example:

```txt
├── examples
    └── example1
        └── main.go
```

## Project Structure Tips

### Package Naming

[Go Package Naming Conventions](go_packages.md#package-naming-conventions)

### Package Nesting

[Go Package Nesting Conventions](go_packages.md#package-nesting-conventions)

### Go libraries

If you are writing a Go library with a single purpose, you might want to have your exported public Go API at the top level.

For example with this file structure:

```s
.
├── .git
├── examples
|   └── example1
|       └── main.go
├── internal
|   ├── package1
|   |   └── package1.go
|   └── package2
|       └── package2.go
├── api.go
├── go.mod
└── go.sum
```

The differences are as follows:

- no `cmd` directory since this is not a runnable application
- no `pkg` directory since this is a library, all exported Go API should be at the top level to reduce the length of import statements
- `api.go` file containing all your Go public API. It should contain your exported interfaces, constants and constructors.

**NOTE:** Keep most of logic inside `internal` directory.

- Most of your code should still reside in the `internal` directory
- You should kep your public Go API to a minimum.

## Resources / References

- [Go Project Structure](https://qqq.ninja/blog/post/go-structure/)
- [Golang Standards - Project Layout](https://github.com/golang-standards/project-layout)
- [Go - Project Structure and Guidelines](https://dev.to/jinxankit/go-project-structure-and-guidelines-4ccm)
- [Official Docs - Go - Internal Packages](https://go.dev/doc/go1.4#internalpackages)
- [Use internal packages to reduce your public API surface](https://dave.cheney.net/2019/10/06/use-internal-packages-to-reduce-your-public-api-surface)
- [Official Docs - Go - Package Names](https://go.dev/blog/package-names)
- [Aviv Carmi - Finding the best Go Project Structure - Part 1](https://avivcarmi.com/finding-the-best-go-project-structure-part-1/)
- [Aviv Carmi - Finding the best Go Project Structure - Part 2](https://avivcarmi.com/finding-the-best-go-project-structure-part-2/)
- [Ben Boyter - How to start a Go project in 2023](https://boyter.org/posts/how-to-start-go-project-2023/)
- [go-project-structure](https://github.com/PerimeterX/go-project-structure)
