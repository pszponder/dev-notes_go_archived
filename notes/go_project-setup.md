# Setting Up a Project in Go

## Steps for Setting up a Project in Go

1. Initialize a new module file in the current directory
2. Setup your project structure
3. Create your project entry point

### Initialize a new module in the current directory

[Go Module Initialization](go_modules.md#initializing-a-go-module)

### Setting up the Project Structure

[Go Project Structure](go_structure-project.md)

### Create the Project Entry Point

The project entry point will typically be a `main.go` file and will look like this:

```go
package main

// import statements here...

func main() {
	// All logic for project goes here...
}
```

**NOTE:** You can have more than one entry point in your project!

- Let's say you wrote a Go project which has some frontend and backend logic.
- You could create two entry points, each which have their own `main.go` entry point files.
- One of these files would be for the server-side application, and the other would contain the client-side application.
- If you have more than one entry point into your project, make sure to store each `main.go` file in its own directory for easier execution and organization.
- For an example of multiple `main.go` files in a project, refer to the `cmd` directory in the [project structure](go_structure-project.md#cmd) notes for Go.

## Summary

1. `go mod init [<module-path>]` to initialize a new Go module.
2. Setup your project structure.
3. Create `main.go` entry point file(s)

## References / Resources

- [How to Start a Go project in 2023](https://boyter.org/posts/how-to-start-go-project-2023/)
