# Go Notes

Overall take on Go so far is it's a nice combination of low level capabilities with simple syntax and concepts. Interface and concurrency concepts are interesting.

[My example go project](https://github.com/dscruggs/go-hello-world)

## Helpful Go Tips and Commands

### Common Go Commands
- `go run <file.go>` - Compile and run a Go program
- `go build` - Compile packages and dependencies
- `go test` - Run tests in current directory
- `go get <package>` - Download and install packages
- `go mod init <module-name>` - Initialize a new module
- `go mod tidy` - Add missing and remove unused modules
- `go fmt` - Format Go source code
- `go vet` - Report likely mistakes in packages
- `go doc <package>` - Show documentation for package

### Useful Resources
1. **Official Resources**:
   - [Go Documentation](https://golang.org/doc/)
   - [Go Tour](https://tour.golang.org/)
   - [Go by Example](https://gobyexample.com/)
   - [Effective Go](https://golang.org/doc/effective_go)

2. **Community Resources**:
   - [Go Wiki](https://github.com/golang/go/wiki)
   - [Awesome Go](https://awesome-go.com/) - Curated list of Go frameworks and libraries
   - [Go cheat sheet](https://devhints.io/go)

## Functions

- OOP in Go is not like python with functions and classes. Instead there's structs, methods, and interfaces and interfaces work with polymorphism. Kind of cool
- Naked returns seem like bad practice
- Go supports first class and higher order functions (can use functions as values)
- Anonymous functions in go also supported
- `Deferring` is a way to evaluate a function when its called but not actually execute it until the surrounding function returns. useful for DB and other connections
- Go is `block scoped` not function scoped like python, so if you declare something in a function or loop or if/switch/select/explicit block its only good there. You can use brackets to make an explicit block
- `Closure` is a function that references variables from outside its own function body
- `Currying` partially applies a function to basically take a function of multiple args and transform it into a sequence of functions. A little confusing on when to apply here but might be useful

## Structs
- anonymous structs supported but doesn't seem like something you should really use
- you can use struct{} to compare and see if all values are default
- embedded structs allow for inheritance
- structs seem like dataclasses in python
- `receivers` are a special type of function parameter, a function with a receiver is a `method`.
- arranging struct fields largest to smallest is more memory efficient

## Interfaces
- `interfaces` are just collections of method signatures. An interface defines the methods a type has to have to use it. Those functions can do anything inside they just need the signature
- the phrasing is "implements the x interface", and anything that implements an interface can be treated as that interface (type)
- you can get the underlying type of an interface by casting, need to do it safely and check for errors. Can also use v := num.(type) with a switch statement to check for interface types


## Errors
- go doesn't have exceptions or any special error types. Errors are just an interface that takes no args and returns a string. You can implement this with any type
- `panic` is used for unrecoverable errors, but I've seen some prefer `log.Fatal` to print a message and exit. I don't know why that's preferred. But panic is definitely not like `try/except` in python


## Loops

```go
for INITIAL; CONDITION; AFTER{
  // do something
}
```
- initial runs once at the beginning of the loop and can create variables in the loop's scope
- condition is checked before each iteration, break if it doesn't pass
- after runs after the iteration
- can omit sections, so like you have have a loop missing condition to run forever. Or omit the initial and after to have a while loop. Can omit everything and just have an infinite loop with no args
- `continue` and `break` keywords for control flow

## Arrays
- arrays are fixed sized and single type
- `slices` are dynamically sized and more flexible. All non-nil slices always have an underlying array but don't always need to be specified
- has `...` operator in functions to accept variadic arguments in functions (basically just a slice of args unspecified). used in functions like string concat where # of args is not fixed
- for iterating over elements in a slice:
```
for INDEX, ELEMENT := range SLICE {
}
```
- slice of slice is a 2d matrix, can have n dimensional slices
- be careful when working with multiple slices pointing to the same underlying array. A common problem is when using append and creating a new slice from that

## Maps
- similar to python dicts
- checking if a key exists `elem, ok := m[key]`
- types that can be used as keys in maps must be comparable (==)
- can use structs as keys for allowing multiple dimensions to define keys
- maps and slices are mutable within functions because they hold references


## Pointers
- same as other languages, just variable that stores a memory address referencing a value
- empty pointers are just nil
- no pointer arithmetic in Go
- since functions pass by value in go most of the time you can pass pointers to manipulate global state within functions
- struct pointers can just be accessed like normal structs interestingly. `msgTotal := analytics.MessagesTotal` is equivalent to `(*analytics).MessagesTotal`
- don't derefernce nil pointers it will cause a panic
- pointer receivers on methods are more common than value receivers because pointers can modify inside the method. Apparently methods with pointer receivers don't require a pointer is used to call the method, it will be automatically derived from the value

## Packages and modules
- in go it's flipped from python. A `module` is a collections of `packages`. Typically 1 module per repo that contains one or more packages. Each package contains one or more Go source files
- `go init` to start a project, `go run` to run go files. `go build` to build executables. `go install` installs it on your machine and it's runable by command line
- in go functions with names starting with a lowercase letter are unexported and private to the package. Functions with uppercase letters are exported/accessed externally

## Concurrency
- `concurrency` means tasks being executed at the same time. It doesn't necessarily mean in parallel because if there's a single core it can still execute tasks concurrently, just constantly switching between them
- concurrency in go just needs you to use the go keyword when calling a function `go doSomething()`
- `channels` are a typed, thread safe queue in go and allow goroutines to communicate with eachother
- can send an empty struct to a channel just as a "hey this finished" type of message, and another caller can just block and wait to see it got sent
- you can add a buffer to a channel in the make command `ch := make(chan int, 100)`
- you need to close channels. Don't send on a closed channel it will panic
- listening to multiple channels. There's also a default value to do things if no values are ready:
```go
select {
case i, ok := <-chInts:
	if ok {
		fmt.Println(i)
	}
case s, ok := <-chStrings:
	if ok {
		fmt.Println(s)
	}
}
```
- there's read and write only channels
- good article on channels https://dave.cheney.net/2014/03/19/channel-axioms
- send to a nil or read from a nil channel blocks forever
- sned to a closed channel panics, read from a closed channel returns 0 value immediately
- a declared by uninitialized channel is nil just like a slice
 

## Mutexes
- go has built in mutexes for locking/unlocking data. Defer is good to use for unlocking in functions so you don't forget to unlock
- things like maps are not thread safe so locking is important
- RLock and RUnlock

## Generics
- Generics in go just allow multiple types to be used (or any type) instead of a specific type for variables/function signatures
- you can create constraints on generics to require them to meet an interface or certain types
- `type-set / type-list interfaces` allow you to specify types that are allowed instead of methods. So this lets you make nice constraints on type parameters. Interfaces can take type parameters as well to enforce constraints on parameters of their methods. T is used a lot of the time for type variables but it's not a requirement

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

## Enums
- no enums in go. You can create type definitions that enforce type requirements on params, but you can't enforce specific values like enums can
- `iota` in go which just creates a sequence of numbers are sometimes used like enums, I'm not sure why

## Reader vs Writer

#### Reader

A `Reader` pulls data from its source into a destination buffer. The caller of Read provides the buffer, and the Reader fills it with data from its source.  
**Example:** A file, string, or network stream is read into a byte array.

#### Writer

A `Writer` pushes data from a source (provided by the caller) into its destination. The caller provides the data, and the Writer decides where to send it (e.g., a file, socket, or memory).  
**Example:**
The data in a buffer is written to a file or a socket.
