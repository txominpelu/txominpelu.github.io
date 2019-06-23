+++
title = "Cgo"
tags = [
    "go",
    "golang",
    "ffi",
    "c"
]
date = 2019-06-10
toc = true
+++


## What is cgo ?

[Cgo](https://golang.org/cmd/cgo/) is go's [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface) for C. 
This means that with Cgo you can call C code from Go and viceversa.

## How does it work ?

Two languages can talk to each other whenever they share a communication channel. For example a not so
efficient implementation of FFI can be done through RPC. However both C and Go are languages that compile
to machine code (in opposition to vm languages like Java). This means that machine code is a shared
representation for both languages, this representation is what Cgo uses to make it possible for both 
languages to interact.


##  Main challenges

### 1. Calling conventions

Even though they are both compiled to the same low level machine code it is not possible to call Go from
C directly, Why ? Because they don't share low level representations for some of their data types (e.g Go
has certain types that cannot be represented in C) and because the machine code representations of their
functions are no compatible: C takes integer and pointer arguments from registers while Go functions take 
arguments and return values through the stack.

That means that when calling C from Go, Cgo has to transform the paremeters from Go data types to data types 
that C can accept and it also has to make sure to write assembly code that prepares the parameters in a format
that a C function can read, as well as receive the return value from the C function and pass it to Go  (this is
done with a trampoline).

**Notes**:

- For those interested in more details, the way that a language accepts parameters and returns values in assembly is called its 
[calling convention](https://en.wikipedia.org/wiki/Calling_convention) the go calling convention is
described [here](https://github.com/golang/go/files/447163/GoFunctionsInAssembly.pdf), according to
[X86_calling_conventions](https://en.wikipedia.org/wiki/X86_calling_conventions) the C calling convention
is called **cdecl** for IA-32 and **System V AMD64 ABI** for x86-64 and it is considered a standard format
for Unix.

- The code in charge of doing the trampoline is available at [cgocall.go](https://golang.org/src/runtime/cgocall.go) and
it contains a thorough explanation of how the whole process works in Cgo.

### 2. Static Typing

When Go calls C functions it needs to check that the right parameters with the right types are being passed. But how does the Go
compiler know what are the types of a go function ? Only a C compiler is capable of knowing what is the signature for a function
defined in a C source file. So if CGo wanted to have type verification for C calls from Go it had to either implement it's own C 
compiler or rely on an existing one. Since maintaining a proper parser for C would be an incredible effort the creators of CGo 
decided to rely on an existing C compiler to obtain the type signature of C functions. The way they do this is by relying on the
error messages generated when trying to compile code trying to call the defined functions []. 

**Notes**:

- The process that Cgo follows to obtain the types of C functions is defined 
[here](https://github.com/golang/go/blob/860c9c0b8df6c0a2849fdd274a0a9f142cba3ea5/src/cmd/cgo/doc.go#L378-L471).

### 3. Garbage collection and Cgo

As you may know Go is a garbage collected language while in C memory is managed by the developer. This represents 
a challenge for CGo since the Go garbage collector needs to know the location of every pointer to be able to free
that memory when it is no longer in use. CGo addresses this problem by imposing constraints on the kind of manipulations
that can be done to the values passed to C functions and viceversa. This rules are checked dynamically and breaking most
of them would cause the program to stop with a panic message. Memory allocated inside C code (e.g calling C.CString()
to create a String in C) it needs to be manually freed since the Go garbage collector is not aware of it and doesn't
know how to handle it. Failing to do so will immediately cause a memory leak in your program.

**Notes**

- The rules required to handle pointer in Cgo are explained [here](https://golang.org/cmd/cgo/#hdr-Passing_pointers)

## Deeper insights into the topic

This article is only meant as a quick introduction to Cgo. For those interested in understanding the topic in more
detail [Filippo Valsorda](https://blog.filippo.io/) has studied the topic and published a bunch of articles and 
presentations that are definitely worth reading:

- [RustGo: Calling rust from go with near-zero overhead](https://blog.filippo.io/rustgo/)
- [Why Cgo is slow?](https://speakerdeck.com/filosottile/why-cgo-is-slow-at-capitalgo-2018)
- [From Cgo back to go](https://speakerdeck.com/filosottile/from-cgo-back-to-go-gophercon-2016)

[Dave Cheney](https://dave.cheney.net/) has also published relevant posts on the topic, specially emphasizing
the limitations of CGo:

- [Cgo is not go](https://dave.cheney.net/2016/01/18/cgo-is-not-go)


# References


General concepts:

- [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface)
- [Calling convention](https://en.wikipedia.org/wiki/Calling_convention)
- [X86_calling_conventions](https://en.wikipedia.org/wiki/X86_calling_conventions)
- [Trampoline](https://en.wikipedia.org/wiki/Trampoline_(computing))


CGo and Golang references:

- [Cgo](https://golang.org/cmd/cgo/)
- [Go Functions in Assembly](https://github.com/golang/go/files/447163/GoFunctionsInAssembly.pdf)
- [A Quick Guide to Go's Assembler](https://golang.org/doc/asm)
- [cgocall.go](https://golang.org/src/runtime/cgocall.go)



