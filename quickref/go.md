# Go Notes

Overall take on Go so far is it's a nice combination of low level capabilities with simple syntax and concepts. Interface and concurrency concepts are interesting.

## Quick links

[Go tour](https://go.dev/tour)  
[Go cheat sheet](https://devhints.io/go)

## Basics

### Types

- has basic number types
- has string type
- has arrays which are fixed
- slices are dynamic arrays
- maps are basically python dictionaries
- structs are collection of fields similar to other languages structs
- Methods are functions that can modify other values or functions

## Random notes

- `Closures` are interesting concept. This is present in a lot of languages where a function creates another function inside it, and calling the sub function will inherit the state of the parent function

### Reader vs Writer

#### Reader

A `Reader` pulls data from its source into a destination buffer. The caller of Read provides the buffer, and the Reader fills it with data from its source.  
**Example:** A file, string, or network stream is read into a byte array.

#### Writer

A `Writer` pushes data from a source (provided by the caller) into its destination. The caller provides the data, and the Writer decides where to send it (e.g., a file, socket, or memory).  
**Example:**
The data in a buffer is written to a file or a socket.

### Constraints

Go supports polymorphism with type constraints on functions that allow you to pass different types to functions as long as they meet the constraints using braces. You can use interfaces to capture many constraints, and Go also has built in constraints like comparable. `any` type allows for generic functions

```go
// Numeric allows all integer and floating-point types
type Numeric interface {
 int | int8 | int16 | int32 | int64 | uint | float32 | float64
}

// Max returns the maximum of two numeric values
func Max[T Numeric](a, b T) T {
 if a > b {
  return a
 }
 return b
}
```

### Concurrency

Concurrency is handled nicely in Go with goroutines and channels, as well as mutexes. Really simple interfaces to make sure concurrency doesn't cause any issues among different routines
