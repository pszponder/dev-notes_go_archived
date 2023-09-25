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

## Create and Use a Makefile

Use `Makefile`s to automate common tasks in your Go project

- Create a `Makefile` in your project's root directory
- [Here](https://www.alexedwards.net/blog/a-time-saving-makefile-for-your-go-projects) is a great article about the Makefile for go and the corresponding [gist](https://gist.github.com/alexedwards/3b40775846535d0014ab1ff477e4a568)

```make
# Change these variables as necessary.
BINARY_NAME := example
MAIN_PACKAGE_PATH := ./

# Set the default target to `run`
.DEFAULT_GOAL := run

# ==================================================================================== #
# HELPERS
# ==================================================================================== #

## help: print this help message
.PHONY: help
help:
	@echo 'Usage:'
	@sed -n 's/^##//p' ${MAKEFILE_LIST} | column -t -s ':' |  sed -e 's/^/ /'

.PHONY: confirm
confirm:
	@echo -n 'Are you sure? [y/N] ' && read ans && [ $${ans:-N} = y ]

.PHONY: no-dirty
no-dirty:
	git diff --exit-code


# ==================================================================================== #
# QUALITY CONTROL
# ==================================================================================== #

## tidy: format code and tidy modfile
.PHONY: tidy
tidy:
	go fmt ./...
	go mod tidy -v

## audit: run quality control checks
.PHONY: audit
audit:
	go mod verify
	go vet ./...
	go run honnef.co/go/tools/cmd/staticcheck@latest -checks=all,-ST1000,-U1000 ./...
	go run golang.org/x/vuln/cmd/govulncheck@latest ./...
	go test -race -buildvcs -vet=off ./...


# ==================================================================================== #
# DEVELOPMENT
# ==================================================================================== #

## test: run all tests
.PHONY: test
test:
	go test -v -race -buildvcs ./...

## test/cover: run all tests and display coverage
.PHONY: test/cover
test/cover:
	go test -v -race -buildvcs -coverprofile=/tmp/coverage.out ./...
	go tool cover -html=/tmp/coverage.out

## build: build the application
.PHONY: build
build:
	@# Include additional build steps, like TypeScript, SCSS or Tailwind compilation here...
	go build -o=/tmp/bin/${BINARY_NAME} ${MAIN_PACKAGE_PATH}

## run: run the  application
.PHONY: run
run: build
	/tmp/bin/${BINARY_NAME}

## run/live: run the application with reloading on file changes
.PHONY: run/live
run/live:
	go run github.com/cosmtrek/air@v1.43.0 \
		--build.cmd "make build" --build.bin "/tmp/bin/${BINARY_NAME}" --build.delay "100" \
		--build.exclude_dir "" \
		--build.include_ext "go, tpl, tmpl, html, css, scss, js, ts, sql, jpeg, jpg, gif, png, bmp, svg, webp, ico" \
		--misc.clean_on_exit "true"


# ==================================================================================== #
# OPERATIONS
# ==================================================================================== #

## push: push changes to the remote Git repository
.PHONY: push
push: tidy audit no-dirty
	git push

## production/deploy: deploy the application to production
.PHONY: production/deploy
production/deploy: confirm tidy audit no-dirty
	GOOS=linux GOARCH=amd64 go build -ldflags='-s' -o=/tmp/bin/linux_amd64/${BINARY_NAME} ${MAIN_PACKAGE_PATH}
	upx -5 /tmp/bin/linux_amd64/${BINARY_NAME}
	# Include additional deployment steps here...
```

```make
# https://www.youtube.com/watch?v=QPfNopc6B_g

BINARY_NAME=<app-name>
.DEFAULT_GOAL := run

build:
	GOARCH=amd64 GOOS=darwin go build -o ./target/${BINARY_NAME}-darwin main.go
	GOARCH=amd64 GOOS=linux go build -o ./target/${BINARY_NAME}-linux main.go
	GOARCH=amd64 GOOS=windows go build -o ./target/${BINARY_NAME}-windows main.go

run: build
	./target/${BINARY_NAME}-linux

clean:
	go clean
	rm ./target/${BINARY_NAME}-darwin
	rm ./target/${BINARY_NAME}-linux
	rm ./target/${BINARY_NAME}-windows

test:
	go test ./...

test_coverage:
	go test ./... -coverprofile=coverage.out

dep:
	go mod download

vet:
	go vet

lint:
	golangci-lint run --enable-all
```

- Replace `<app-name>` with your app name on the 1st line
- _NOTE:_ Add an `@` symbol on any of the lines with instructions to prevent them from showing up in the console output
- _NOTE:_ To comment out lines in a `Makefile`, use `#` at the beginning of a line
- **_USAGE:_** `make <instruction-set>` (ex. `make build` or `make clean`)
  - Line 2 specifies the default instruction set to run when you type `make` in the terminal
  - In the example above, the `run` command is set to the default. So running `make` in your cli will result in `make run` being executed (`make run` has the `build` instruction set as a dependency so whenever you run `make run`, `make build` is run first)

## Summary

1. `go mod init [<module-path>]` to initialize a new Go module.
2. Setup your project structure.
3. Create `main.go` entry point file(s)
4. _OPTIONAL:_ Create and use a `Makefile`

## References / Resources

- [How to Start a Go project in 2023](https://boyter.org/posts/how-to-start-go-project-2023/)
- [GNU Make](https://www.gnu.org/software/make/)
- [Alex Edwards - Quick tip: A time-saving Makefile for your Go projects](https://www.alexedwards.net/blog/a-time-saving-makefile-for-your-go-projects)
- [Earthly - Makefile for Golang](https://www.youtube.com/watch?v=QPfNopc6B_g)
- [Earthly - Makefiles Playlist](https://www.youtube.com/playlist?list=PLtimIuw_6KzXti4B_Y6xIEj88YlstpCnk)
