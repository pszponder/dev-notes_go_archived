# Go Modules

Go Modules commonly consist of one project or library and contain a collection of Go packages that are then released together.

## What is a Go Module?

A Go `module` is a **_collection_** of related go `package`s in the same directory with built-in dependencies and versioning.

A `module` contains with two additional special files:

- `go.mod`
- `go.sum`

## Package vs Module (as Pertaining to Go)

- A [`package`](go_packages.md) is a collection of Go **_source files_** (`.go`) in the same directory.
- A `module` is a collection of Go `packages` with built-in dependencies and versioning

## Initializing a Go Module

```bash
# Initialize a new module in the current directory
# This creates the go.mod & go.sum files
go mod init [<module-path>]
```

- The module path usually corresponds to a repository location where the module is hosted or where you plan to publish your module to.
- Ex. `github.com/<username>/path/to/module`
- You don't need to create a repo and have code pushed to it before initializing a module.
- If you don't include the `<module-path>` in `go mod init`, the parent directory will be used as the `module path`.

**NOTE:** In Go, `module path == project path`

> In most cases, your module path doesn't need to be a 'real' functioning URL. It's really just an arbitrary string which acts as a unique identifier for your module.

## Updating / Cleaning go.mod & go.sum

```bash
# Clean / Update the go.mod & go.sum files
#  of any unused modules
go mod tidy
```

## go.mod

The `go.mod` file specifies / defines:

- module's module path, (which is also the import path used for the root directory)
- project name
- Go version
- package dependencies of the Go project

`go.mod` is just like JavaScript's `package.json`, it describes the go module.

The `go.mod` file contains:

- Name of the module
- Module / Project path
- Version of Go used
- Package Dependencies used by module

Example of typical `go.mod` file

```txt
// Defines the Module Path
module example.com/mymodule

// Defines the version of Go to use for this module
go 1.14

// Define Module dependencies and their versions
require (
    example.com/othermodule v1.2.3
    example.com/thismodule v1.2.3
    example.com/thatmodule v1.2.3
)

replace example.com/thatmodule => ../thatmodule
exclude example.com/thismodule v1.3.0
```

### Module Path / Project Path

`module path` is often the repo url

- The Module Path defines the import path prefex for all module packages.
- It is typically the repository url (without the `https://` prefix)
  - `github.com/<username>/<repo-name>`
  - `golang.org/x/tools` would represent the url path `https://golang.org/x/tools` which the go command would use to lookup and download the module from.

**NOTE:** A module can be defined locally without belonging to a repository.

### Module Name / Project Name

The `module name` / `project name` is the last piece of the `module path` / `project path`

Ex. If your module path is `github.com/<username>/tools` than your `module name` will be `tools`.

### Package Import Path

An `import path` is a string used to import a package

- A package's import path is its `module path` joined with its subdirectory within the module
- Ex. the module `github.com/google/go-cmp` contains a package in the `cmp/` sub-directory. That package's `import path` is `github.com/google/go-cmp/cmp`.
- Packages in Go's standard library do not have a `module path` prefix.

## Go.sum

The `go.sum` file contains all of the chechsums for the package dependencies of the `module`

**CAUTION:** Don't manually alter the `go.sum` file!

- The `go.sum` file is intended to be readonly and should only be updated by Go using commands such as `go mod tidy`
- The checksum present in `go.sum` file is used to validate the checksum of each direct or indirect dependency to confirm that node of them have been modified

## Working with Multiple Packages

## References / Resources

- [Official Docs - Using Go Modules](https://go.dev/blog/using-go-modules)
- [Official Docs - Tutorial: Create a Go module](https://go.dev/doc/tutorial/create-module)
- [Official Docs - Go.mod file reference](https://go.dev/doc/modules/gomod-ref)
- [Official Docs - How to Write Go Code](https://go.dev/doc/code)
- [How to use Go Modules for Package Management](https://www.workfall.com/learning/blog/how-to-use-go-modules-for-package-management/)
- [How to Use Go Modules](https://www.digitalocean.com/community/tutorials/how-to-use-go-modules)
- [Techworld with Nana - Golang Tutorial for Beginners | Full Go Course](https://www.youtube.com/watch?v=yyUHQIec83I)
- [NerdCademy - GoLang Packages and Modules | Beginners Go Tutorial](https://www.youtube.com/watch?v=nLaxs5w9bZc&list=PLujhHB_uAFJws6Vv5q1KDoaQ4YcpS9UOm&index=21&t=20s)
- [Now to Go Modules - Just tell me how to use Go Modules!](https://engineering.kablamo.com.au/posts/2018/just-tell-me-how-to-use-go-modules/)
- [Alex Edwards - An introduction to Packages, Imports and Modules in Go](https://www.alexedwards.net/blog/an-introduction-to-packages-imports-and-modules)
