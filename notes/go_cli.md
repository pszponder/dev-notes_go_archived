# Go CLI Tool

## Using Go's Help Command

```bash
go help <go-command-name>
```

## Go main view

```bash
# Display all options in go cli
go
```

## Commands for Go Module (go mod ...)

### go mod init

Create a new module in the current directory

```bash
# Initialize a new module file (go.mod) in the current directory
go mod init [<module-path>]
```

> NOTE: THe module path can correspond to a repository you plan to publish your module to (e.g. `github.com/<username>/<repo>`)

### go mod tidy

Fix missing modules and remove others which aren't in use.

Use `go mod tidy` whenever your module dependencies change.

```bash
go mod tidy
```

### go mod download

Download modules to your device's cache

```bash
go mod download
```

### go mod vendor

Create a vendored copy of dependencies

```bash
go mod vendor
```

### go mod graph

Print module requirements graph

```bash
go mod graph
```

### go mod verify

Verify dependencies have expected content

```bash
go mod verify
```

### go mod why

Explains the need for packages or modules

```bash
go mod why
```

## Compiling and Executing a Go Project / Module

### go run (Compile and Execute Go Program)

Use `go run` to compile and execute the specified file / list of files

- Use this command when testing / developing your code
- `go run` compiles code to temporary location
- Using `go run` compiles and runs the named `main` Go package.
- The compiled files are placed in a temporary file location and are deleted after the code is executed.
- This command is useful for testing small programs during the initial development stage of your application
- If you wish to maintain a permanent copy of your compiled code, use [`go build`](go_cli.md#go-build)

```bash
go run [<file-name(s)>] [--work]
```

- `--work` view the location of the temporary build files

> NOTE: Use `go run .` to run all files in the current directory. This avoids having to write out all of the files in the current directory.

```bash
# Run all files in the current directory
go run .
```

### go build

Use `go build` to compile your module code in the current directory

```bash
go build [<module-name>] [-race]
```

> NOTE: The Default binary name is based on `module name`

- When you run `go build`, the default is for Go to automatically decide on the name of the generated executable based on the module name

Options:

- `-o [<new-file-path/new-executable-name>] [<module-name>]` to change the file path (and/or file name) to where the compiled code is stored
- `-race` to check for concurrency problems

### go install

`go install` command compiles your code and then installs the binary onto your system so that you can access it from anywhere

```bash
go install [<module-name>]
```

> NOTE: Default install path when using `go install`

- The default path of the binary file when using `go install` is the `$GOPATH/bin` directory

> QUESTION: How to find `$GOPATH`?

- Type `go env GOPATH` into your terminal
- The default is the `go` directory inside of your `$HOME` directory: `$HOME/go`

Example of using `go install`:

```bash
# This command installs the "hello" module onto your system
go install example/user/hello
```

If you run `$GOPATH/bin/<executable-name>` in your terminal, your compiled binary code will execute on your machine.

## Format Go Code

```bash
# Format all source files
go fmt
```

```bash
# Format source files in specific directory
go fmt <dir-path-to-go-file(s)>
```

## Run Project's Test Suites

```bash
go test
```

## Resources / References

- [ZTM - Go Programming (Golang): The Complete Developer's Guide](https://zerotomastery.io/courses/learn-golang/)
- [go run vs go build vs go install](https://levelup.gitconnected.com/go-run-vs-go-build-vs-go-install-c7c0fd135cf9)
- [DigitalOcean - How to Build and Install Go Programs](https://www.digitalocean.com/community/tutorials/how-to-build-and-install-go-programs)
