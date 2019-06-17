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
described [here](https://github.com/golang/go/files/447163/GoFunctionsInAssembly.pdf), according to
[X86_calling_conventions](https://en.wikipedia.org/wiki/X86_calling_conventions) the C calling convention
is called **cdecl** for IA-32 and **System V AMD64 ABI** for x86-64. Without getting into too much detail
about how those two conventions work it is easy to understand that is not possible to call a C function
without any kind of wrapper from Go if we consider that the most common C convention (the already mentioned
System V AMD64 ABI) takes integer and pointer arguments from registers while Go functions take arguments
and return values through the stack.

Therefore one of the functionalities of Cgo is to provide a wrapper around C code. Let's imagine that we
want to call a C function from go:

```c
int plusone(int value)
{
    return value + 1;
}
```

## Garbage collection and Cgo



## Notes

- This article has taken many of it's references from: [RustGo: Calling rust from go with near-zero overhead](https://blog.filippo.io/rustgo/)

## References
- [Cgo](https://golang.org/cmd/cgo/)
- [FFI](https://en.wikipedia.org/wiki/Foreign_function_interface)
- [Calling convention](https://en.wikipedia.org/wiki/Calling_convention)
- [X86_calling_conventions](https://en.wikipedia.org/wiki/X86_calling_conventions)

Assembler go:

- [Go Functions in Assembly](https://github.com/golang/go/files/447163/GoFunctionsInAssembly.pdf)
- [A Quick Guide to Go's Assembler](https://golang.org/doc/asm)



