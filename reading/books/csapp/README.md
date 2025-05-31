# Computer Systems: A Programmer's Perspective

by Randal E. Bryant and David R. O’Hallaron

Lots of great info on the website <https://csapp.cs.cmu.edu/>

### [Chapter 1: A tour of computer systems](./01_tour_of_comp_sys.md)

Goes over the basics of computer systems and how they work.

Major takeaways:

- Hardware and software are complementary to making systems work
- Abstractions are important to simplify using the systems
- I/O is the bottleneck for most systems, not CPU ops
- Storage devices have hierarchies because larger storage is slower and more expensive
- OS serves as intermediary between software and hardware
- Networking is just another I/O device for computers to communicate with each other

## Part 1: Program Structure and Execution

### [Chapter 2: Representing and Manipulating Information](./02_representing_and_manipulating_info.md)

Covers data types and details and integer and floating point computation

- computers encode information as sequences of bits, usually organized into bytes/words. Different computers use different encodings to represent those into things like integers, real numbers, and characters
- goes into details on representations for integers, characters, and floating point numbers
- be aware of two's complement and floating point ops implications on computations
- In C when you cast there's not a whole lot of actually transformations happening under the hood just telling the machine to interpret the bits differently. Exception is when converting from higher to lower precision numbers

## Chapter 3: Machine-Level Representation of Programs

## Chapter 4: Processor Architecture

## Chapter 5: Optimizing Program Performance

## Chapter 6: The Memory Hierarchy

## Chapter 7: Linking

## Chapter 8: Exceptional Control Flow

## Chapter 9: Virtual Memory

## Chapter 10: System-Level I/O

## Chapter 11: Network Programming

## Chapter 12: Concurrent Programming
