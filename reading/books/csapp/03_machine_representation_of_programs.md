# Chapter 3: Machine-Level Representation of Programs

- Computers execute `machine code` (sequences of bytes) that encode operations to manipulate data, manage memory, read and write data on storage devices, and communicate over networks
- A compiler generates machine code based on the rules of the programming language, the instruction set of the target machine, and the conventions followed by the operating system
- GCC C compiler generates its output in the form of `assembly code` which is a textual representation of machine code
    - Then invokes an `assembler` and a `linker` to generate the executable machine code from the assembly code
- High level languages shield details of the machine-level implementation from its users, but when writing in assembly programmers have the specify the low level instructions
- Also writing in high level languages let you compile to different machines whereas assembly is very machine specific
- Important to understand machine code because it's what is actually running. So for cases where optimization or security or advanced debugging is necessary machine code is useful to be able to read
- This chapter will cover the transformations from programming language to assembly that compilers do
