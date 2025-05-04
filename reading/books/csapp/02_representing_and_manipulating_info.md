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
- Compiler and run time system partitions the memory space into units to store `program objects` (program data, instructions, and control info)
- C compiler associates a `type` with each object which generates machine code to access the value stored at the given address
- At the machine level, there's no types. Just bytes and bits

### Hexidecimal notation

- An 8 bit bytes ranges from 00000000 to 11111111 in base 2
- `Hexidecimal` is base 16. Each number 0 through 9, followed by A through F to get to 16 values. So a byte ranges from 00 to FF
- In C numberic constants startinv with 0x or 0X are interpreted as hexidecimal
- Converting between hexidecimal and binary is easy because each hex digit represents 4 bits. Converting from binary to hex you just group the bits into groups of 4 from the least significant bit (right to left assuming the system is little endian) and pad with leading 0s on the left if necessary to get to groups of 4. No need to flip the order of the bits or anything though.

```
Example of hex to binary:0x173A4C -> 0001 0111 0011 1010 0100 1100
```

Converting from binary or hex to decimal is just the same as converting from any other bases. Just multiply each digit by the base to the power of the digit's position.

```
Example of hex to decimal: 0x173A4C -> 1 * 16^5 + 7 * 16^4 + 3 * 16^3 + 10 * 16^2 + 4 * 16^1 + 12 * 16^0 = 1522252
```

Converting from decimal to hex or binary is the reverse. Divide the number by the base and take the remainder, then repeat with the quotient until you get to 0. The remainders are the digits in the new base, from least significant to most significant. This feels a little counterintuitive but the first digit is the most significant because it's the highest power of the base, even though it will be the lowest quotient.

```
Example of decimal to hex:
314,156 = 19,634 . 16 + 12 (C)
19,634 = 1,227 . 16 + 2 (2)
1,227 = 76 . 16 + 11 (B)
76 = 4 . 16 + 12 (C)
4 = 0 . 16 + 4 (4)
```

So 314,156 in decimal is 0x4CB2C in hex.

Doing arithmetic in hex is the same as doing it in decimal. Just add the numbers together and carry the 16s. Align the numbers by the least significant digit and add them together.

### Data sizes

- `word size` is the number of bits in a word. A word is the natural size of the machine's data path, or the nominal size of pointer data. The virtual address space is encoded by words, so the word size determines the size of the address space. The address space goes from 0 to 2^wordsize - 1, giving 2^wordsize addresses/bytes.
- Systems shifted from 32 bit to 64 bits in the last decade or so. Most 64 bit systems can run 32 bit programs, but most 32 bit systems can't run 64 bit programs.
- Different data types have different sizes allocated in memory. Signed vs unsigned data types have the same size, but unsigned can store a larger range of numbers since it doesn't have to represent negative numbers.
- To help make programs more portable, you should try to make the program insensitive to the exact size of the data types.

### Addressing and Byte Ordering

- For programs that span multiple bytes you have to establish:
  - What the address of the object will be
  - How the bytes are ordered in memory

Almost all systems store as a contiguous sequence of bytes with the address being the smallest address of the sequence.

- `Little endian` is when the least significant byte is stored at the smallest address.
- `Big endian` is when the most significant byte is stored at the smallest address.

```
Examples of storing 0x12345678 in memory from smallest address to largest:

Example of little endian:
0x12345678 -> 78 56 34 12

Example of big endian:
0x12345678 -> 12 34 56 78
```

Funny fact: `endianness` came from the book "Gulliver's Travels" by Jonathan Swift. The Lilliputians and the Blefuscuans had a war over which end of an egg to crack. The Lilliputians were big endians and the Blefuscuans were little endians.

Most modern systems are little endian. Hardware designers have hardware that can support both many times, but OS and other software on them pick one or the other. The choice is debated but really all that matters is that things are consistent.

Potential issues with endianness:

- **Networking** - if you need to communicate between big and little endian systems this could cause problems. For this reason networking protocols were established to convert from internal representation on systems to a standard representation on the network.
- **Integer byte sequences** - You need to know the endianness of the system to interpret bytes as integers. Can come up in debugging machine code.
- **Casting** - If you cast a pointer or data type to a different type, you need to know the endianness of the system to interpret the bytes as the correct type.

- using `sizeof` to determine the size of a data type or variable in bytes rather than hardcoding the size in bytes helps to make the program more portable.
