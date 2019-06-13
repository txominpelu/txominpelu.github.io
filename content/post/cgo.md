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
to machine code (in opposition to vm languages like Java), meaning they both share representation in some 
sort of assembly code. This shared representation is what Cgo uses to make it possible for both languages
to interact.

### Calling conventions

Even though both C and Go are compiled to assembly they don't use assembly in the same way. For example
they both define their higher level functions in terms of different lower-level assembly primitives. The
way that a language accepts parameters and returns values in assembly is called its 
[calling convention](https://en.wikipedia.org/wiki/Calling_convention) the go calling convention is
described [here](https://github.com/golang/go/files/447163/GoFunctionsInAssembly.pdf). 

(wrapper to go from one to the other) 



## Notes

- This article has taken many of it's references from: [RustGo: Calling rust from go with near-zero overhead](https://blog.filippo.io/rustgo/)

## References
- [Cgo](https://golang.org/cmd/cgo/)
- [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface)
- [Calling convention](https://en.wikipedia.org/wiki/Calling_convention)

Assembler go:

- [Go Functions in Assembly](https://github.com/golang/go/files/447163/GoFunctionsInAssembly.pdf)
- [A Quick Guide to Go's Assembler](https://golang.org/doc/asm)



