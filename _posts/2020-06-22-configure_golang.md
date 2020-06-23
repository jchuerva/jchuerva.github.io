---
title: Configure Go with Homebrew
date: 2020-06-22 18:00:00 +01:00
tags: [go, golang]
description: Configure Go with Homebrew
---

## Install Golang with Homebrew

```
brew update
brew install golang
```

### Check the Go version
```
go version
```

### Add some environment variables to `.zshrc`
```
export GOPATH=$HOME/go
export GOROOT=/usr/local/opt/go/libexec
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOROOT/bin
```

### Uninstall Go
```
<!-- installed by Golang package -->
rm -rvf /usr/local/go/
<!-- installed by homebrew -->
rm -rvf /usr/local/go/libexec
```


### Links:
- https://sourabhbajaj.com/mac-setup/Go/README.html

### To check:
- Control version: https://github.com/moovweb/gvm


### Execution of a file
Having a `hello.go` file:
```
go build hello.go
```

Creates the executable. Then, to execute it:
```
./hello
```

### Execute the go tests
Having the file `hello_test.go`

To execute it:
```
go test
```