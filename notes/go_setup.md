# Setting Up Go on your Machine

## Installing Go

Follow the instructions [here](https://go.dev/doc/install)

**NOTE:** Instead of adding `/usr/local/bo/bin` to the `PATH`, follow the below instructions instead which includes the above but also updates `GOPATH`

## Updating Go Env Vars

```bash
# Check if following variables are set
go env GOROOT # where go runtime is installed
go env GOPATH # where local go projects are stored
```

Add the following to your `.zshenv` file (or wherever you store your env variables such as `.zshrc` or `.bashrc`).

**NOTE**: You can change your `GOPATH` to wherever you want

```txt
# GOLANG SETUP
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

## Check Everything is Installed Correctly

```bash
# Check version of go
go version
```

```bash
# Check that these variables are defined
go env GOROOT
go ENV GOPATH
```

## Go Version Manager (GVM)

**CAUTION:** When you set the go version using `gvm`, it is only active for the active terminal session. Starting a new terminal session will revert the `GOROOT` env variable. If you wish to use `gvm`, each time you set the go version using the `eval` command, you will have to update your `GOROOT` env variable.

- You most likely don't actually need to use `gvm`, just use the standard install instructions to keep the most recent version of GO on your developer machine.

You can install [gvm](https://github.com/andrewkroh/gvm) to manage your version of go for you instead of manually installing.

```bash
# Install a version of go using gvm
gvm install 1.21.1

# Use the version of go from gvm
eval "$(gvm 1.21.1)"

# Check that using go from gvm
# /home/<user>/.gvm/versions/go<version>
go env GOROOT
```

## Resources / References

- [Go - Download and Install](https://go.dev/doc/install)
- [Ben Davis - Downloading Go and Setting up you PATH from Scratch!](https://www.youtube.com/watch?v=Q7uh85_i0-M)
- [Golang Dojo - Go Environment Variables Explained in 5 Minutes](https://www.youtube.com/watch?v=Ut-NLq6d694)
- [gvm](https://github.com/andrewkroh/gvm)
