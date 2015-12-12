# gopath
A Go library for nicer ways to cope with errors while working on the filesystem.

Do the work first -- check for errors later.

This library realizes the recommondations from the blog.golang.org article
[Errors are values](https://blog.golang.org/errors-are-values).
For even more information, also have a look at [Error handling and Go](http://blog.golang.org/error-handling-and-go).

[![Build Status](https://travis-ci.org/fxnn/gopath.svg?branch=master)](https://travis-ci.org/fxnn/gopath)
[![GoDoc](https://godoc.org/github.com/fxnn/gopath?status.svg)](https://godoc.org/github.com/fxnn/gopath)

## Usage

Instead of doing a lot of error handling between each step ...

```go
var err error
var p = "/my/path/to/somewhere"

if p, err = filepath.Abs(p); err != nil {
  // handle error
}
if p, err = filepath.EvalSymlinks(p); err != nil {
  // handle error
}
if p, err = filepath.Rel(p, "other/path"); err != nil {
  // handle error
}

// go on
```

...gopath allows you to write the operations first, and do the error checking later.

```go
var p = gopath.FromPath("/my/path/to/somewhere").Abs().EvalSymlinks().Rel("other/path")

if p.HasErr() {
  // handle error
}

// go on
```

The idea: the GoPath object encapsulates a path _and_ an error object.
Now, as soon as an error occured, each operation turns to a no-op.
That's why it suffices to check for errors in the end.


## Enhanced usage

GoPath has an extension mechanism that allows you to chain function calls with your own code.
The first step is to define your own transformation function:

```go
func normalizePath(p gopath.GoPath) gopath.GoPath {
	return p.Abs().Clean()
}
```

Now, you are able to use GoPath's `Do` method:

```go
var p = gopath.FromPath("/some/path").Do(normalizePath)
```


## Development

While this project embeds functions from the [os](https://godoc.org/os),
[path](https://godoc.org/path) and [filepath](https://godoc.org/path/filepath) packages,
it is far from being complete.
Pull requests are welcome!

Please note, that I split the functionality into several files.
I hope you find their names to be quite self-explanatory.
We have

* `gopath.go`: typedef, constructor and elementary functions
* `err.go`: functionality around error handling
* `transform.go`: transformations from GoPath to GoPath objects
* `terminal.go`: transformations from GoPath to other values
* `predicate.go`: functions on GoPaths with boolean results
* `assert.go`: assertions just return an error object if they fail


## License (MIT)

Licensed under the MIT License, see [LICENSE](LICENSE) file for more information.
