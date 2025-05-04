# Chapter 2: Representing and Manipulating Information

Everything in computers is represented as bits. Binary system is base 2, decimal is base 10.

Bits make everything easier for computers because it's easier to tell apart than if you have multiple states.

- Presence or absence of a hole in a punched card
- Low or high voltage on a wire
- Magnetic orientation clockwise or counterclockwise

All information on computers is just interpretations of sequences of bits. Including letters and numbers.

Numbers:

- Unsigned and signed (twos complement) integers
- Floating point numbers
- Different systems use different representations for numbers and can store different ranges of numbers
- `Overflow` is when you try to store a number that is too large for the system to handle. Lots of methods to handle this.

## 2.1 Information Storage

- Computers store information in `bytes` as the smallest addressable unit of memory. A byte is 8 bits.
- A program views memory as a very large array of bytes in the `virtual address space`, where each byte has a unique address (just a number). The underlying hardware could be a combination of DRAM, flash storage, disk, special hardware, etc. but has the appearance of a single large array of bytes to the program/user.
