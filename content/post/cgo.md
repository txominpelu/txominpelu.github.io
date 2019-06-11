+++
title = ""
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

[Cgo]() is go's [FFI]() for C. This means that with Cgo you can call C code from Go and viceversa.

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
[calling convention](). (wrapper to go from one to the other) 




## References
[Cgo]():
[FFI](https://en.wikipedia.org/wiki/Foreign_function_interface)
[calling convention](https://en.wikipedia.org/wiki/Calling_convention)



