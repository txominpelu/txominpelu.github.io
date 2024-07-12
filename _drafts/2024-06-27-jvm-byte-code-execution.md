---
layout: post
title:  "Hotspot JVM's - Bytecode execution"
date:   2024-06-27 10:00:00 +0200
categories: jvm bytecode execution
---

## How does the JVM execute bytecode instructions ?

- It compiles bytecode instructions into assembler instructions that are dependent
on the architecture where the virtual machine is running.

- The assembler instructions can come from
    - The template interpreter 
    - Code optimized by the C1 compiler
    - Code optimized by the C2 compiler

## The template interpreter 

The template interpreter is based on a table that maps bytecode instructions
to their assembler translations.

At startup the template interpreter builds the table with the mappings.

Then the code gets executed by executing one by one the bytecode instructions of the main
method of the source class. In the abscence of C1 or C2 optimization, the template interpreter
plainly executes the bytecode instructions by their mappings to assembler code.

## Notes on how to debug

 - [Debugging the OpenJDK JVM interpreter in Linux x86_64][debugging-hotspot-interpreter] provides a good 
 introduction to how the template interpreter works. It also provides a tip to debug the generated assembly
 code, by:  

  - Setting a breakpoint once the bytecode -> assembler table is built
  - Identifying the memory instruction that we want to debug (`p _normal_table.entry(Bytecodes::Code::_monitorenter).entry(TosState::atos)`)
  - Setting a breakpoint in lldb with the memory address returned by the previous command (`br s -a 0x000000013fd4bc84`)

 - It seems that another approach is to add a breakpoint in the assembler code that is generated (e.g `MacroAssembler::os_breakpoint` - macroAssembler_x86_64.cpp )

 - The `-XX:PrintInterpreter` flag shows the template interpreter machine code for every bytecode instruction. It is very useful to [install](https://github.com/openjdk/jdk/tree/master/src/utils/hsdis) `hsdis` (run `bash configure <other-optons> --with-llvm=/opt/homebrew/opt/llvm/ --with-hsdis=llvm` ). With `hsdis` the machine code for every instruction is disassembled.

# References

[Bytecode to Assembler - TemplateInterpreter]: http://progdoc.de/papers/Joker2014/joker2014.html#(4)
[brief-intro-template-interpreter]: https://albertnetymk.github.io/2021/08/03/template_interpreter/
[debugging-hotspot-interpreter]: https://martin.uy/blog/debugging-hotspot-openjdk-jvm-x86_64-interpreter-in-linux/
