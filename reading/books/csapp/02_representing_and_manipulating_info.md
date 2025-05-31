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

### Comparing var representations on different OS

- int and float are the same besides byte ordering (Sun is big endian linux, mac, and windows are little endian)
- Pointers are completely different because each OS uses different conventions for storage allocations. Linux 32 and windows use 4 byte addresses, linux 64 uses 8-byte addresses

- the same number in int and float format is represented differently, that'll be covered later

#### Representing Strings

- text data is more platform independent than binary data because of standard encodings such as ASCII. Binary data is OS and hardware dependent

#### Representing Code
- Code instructions are not really cross compatible since it's binary instructions and is OS and hardware dependent

#### Boolean algebra
- George boole started working on 0 and 1 in the 1800s, that's where `boolean` is from
- Key here is you can use 0 and 1 and true and false / logical operators in the same way. Can represent 1 as true and 0 as false and then use operators like `and`, `or`, `not` and `exclusive or`
- can also extend this to vectors of 0s and 1s. Like `1001 & 1110 = 1000`
- Can represent finite sets with binary vectors. Like `{0, 3, 5, 6}` can be represented as `[01101001]` (read right to left)
- can also represent things like signals or colors (3 length vector representing RGB)
- commonly used for masking operations

#### Logical operations
- different than bit-level operations, but they treat anything not 0 as a 1, and 0 as 0 for those logical operations
- when all args are either 0 or 1 it will have identical behavior to bit-level operations

#### Shift operations in C
- `shift` moves bit patterns left or right
- left shift moves bits left x bits and fills with 0
- right shift can either be `logical` which fills the left end x bits with 0s, or `arithmetic` which fills the left end with x repetitions of the most significant bit. Arithmetic shift is apparently useful for operating on signed integers
    - be careful about this because these differences are a portability risk if doing bit shifitng on signed data. Most do arithmetic shifts though. For unsigned data it has to be logical shift
    - some programming languages have specific syntax to distinguish between the 2 like java

### Integer representatins

- `integral` data types means ones that can represent finite ranges of integers
- `long` is t- erences between 32 and 64 bit systems so watch out for this
- unsigned can store basically twice as large positive numbers since they don't need to represent negative numbers

#### Unsigned integers
- just a weighted sum by powers of 2 based on placement in the vector. Standard binary to decimal conversion basically

#### Two's complement encoding
- most common way to represent unsigned. It uses the most significant bit as a subtraction from the number calculated in the same way as unsigned. This most significant bit is called the `sign bit`
- this is why the value is 1 less in the negative direction cause it needs to subtract across 0
- all bit patterns of twos complement are also unsigned representations of integers, so important to know the type if it's signed or unsigned
- `ones complement` and `sign magnitude` are other encodings, but mostly on older machines. Twos complement is the broad favorite today

#### misc notes on integer encodings
- don't assume a valid range of numbers across systems. This helps make code compatible on many platforms and OS
- However, twos complement ranges are a pretty fine assumption, will still be broadly compatible
- Languages like Java have requirements on data type sizes that help it be more cross compatible

#### Conversions between signed and unsigned
- so basically casting in C doesn't change the underlying representation of the data, only how it is interpreted. So going from signed to unsigned or vice versa just keeps the same bits and interprets it differently
- you can just think of it as converting signed to binary, then converting binary to unsigned and vice versa for unsigned to signed
- can lead to strange numerical behavior (-12345 converts to 53191 in unsigned) but is consistent bitwise
- C will do some implicit casting like when assigning variables, it will be casted to the type of the variable being assigned to
- For operations it will convert signed to unsigned if there's mixed types

#### Expanding bit representation of a number
- not possible when the conversion is to a smaller data type and the number is too larger for that. But conversion to a large representation should always be possible
- `zero extension` is just padding leading 0s to the representation of an unsigned number
- for two's complement you can do `sign extension` which adds copies of the most significant bit to the representation

#### Truncating numbers
- drops the highest order bits to truncate. For unsigned numbers this is pretty easily predictable
- for two's complement you truncate and convert the highest order bit to the sign bit
- good exercise on 2.24 that I should do to understand this

#### Signed vs unsigned usage
- lots of languages don't support unsigned because they can cause some of the weird behavior and implicit conversions that can lead to bugs
- be careful when using them, can often times be more trouble than they're worth

### Integer Arithmetic

- takeaway here is there can be weird behavior if you don't pay attention to data type limitations and signed vs unsigned nuances

#### Unsigned addition
- Adding 2 numbers together could require 1 additonal bit than is allocated for those numbers individually
- With a given word size you cannot place a strict limit on the word size to fully represent the results of arithmetic
- Lisp supports `arbitrary size` arithmetic to allow integers of any size within memory limits of the computer
- Most ther languages use `fixed-size` arithmetic
    - Numbers can be truncated if they exceed the word size
- addition is normal unless `overflow` happens meaning it goes above the accepted word size, then it will get subtracted by 2^w. So actually this overflow value will probably be less than the max possible value
- overflows might not trigger errors so need to watch out for strange behavior

#### Twos compliment addition
- need to handle when numbers are both too large or too small to represent
- basically you just add and then truncate to w bits and view as a twos complement number
    - subtracts 2^w if there's `positive overflow` and adds 2^w if there's `negative overflow`
- the two's compliment sum has the exact same bit level representation as unsigned sum, so most computers use the same machine instruction for both
- interesting practice problems on detecting if two signed or two unsigned integers can be added without overflow
- negations of twos complement can be done by taking the compliment of the binary representation and then incrementing by 1

#### Unsigned Multiplication
- again just take the lower order w bits from the result

#### Twos compliment multiplication
- take lower order w bits, highest order bit becomes the sign bit
- again there's bit level equivalence between unsigned and twos compliment

#### Multiplying by constants
- multiplication is slower than addition, subtraction, bit-level operations, or shifting. So compilers try to convert multiplication to shift and addition ops
- one cool trick is multiplication by powers of two can just be replaced by bit shifting left by k bits where k is the power of 2. so multiplicaiton by 4 is just shifting the number left by 2 bits and padding zeros
- Compilers will use similar tricks to convert multiplications by constants to several addition and shift operations

#### Dividing by powers of 2
- Division is even slower than multiplication on most machines
- Dividing by 2 can be done with right shifts, using logical and arithmetic shifts for unsigned and signed respectively
- integer division always rounds towards zero
- can't really express division by arbitrary constants k in terms of divison by powers of 2 to get similar speedups to multiplication

#### Final thoughts on integer arithmetic
- integer arithmetic is really just modular arithmetic. Basically just need to worry about overflow, signed vs unsigned, and know about clever tricks for multiplication
- also remember for speed do add, subtract, shift. Multiplication is slow (but can usually be sped up by compilers), and division is even slower with little to be done to speed it up

### Floating point
- floating point numbers are useful for very large or very small numbers and can be better used for real world arithmetic
- Up until the 80s everyone had their own conventions for how computers represented floating point numbers
- in 1985 the IEEE standard came up which makes floating point applications much more portable

#### Franctional binary numbers

- in decimal notation we write fractional values as basically just a reverse of whole numbers to the right of the decimal. So 100 is `0 x 10^0 + 0 x 10^1 + 1 x 10^2`. 0.001 is `0 x 10^-1 + 0 x 10^-2 + 1 x 10^-3`
- For binary it's the same thing just powers of 2 instead of 10. 
- But this makes representing a wide range of values for a given bit size difficult because you need to continuously lengthen the binary representation to get an adequate approximation of decimal numbers. 
- Also takes a ton of storage if you want to represent very large numbers. Like 5 x 2^100 would be 101 with 100 zeros after it in binary

#### IEEE Floating-Point Representation
- Idea here is rather than store the full value of 5 x 2^100 what if we just stored the 5 and 100?
- adding in a sign for negative and positive gives us 3 values: `(-1)^s x M x 2^E`
    - `s = sign` determines positive or negative and is either 0 or 1. a single bit regardless of system.
    - `M = mantissa` (or significand) is the biggest part of this representation and is a binary fractional representation of a number between 1 and 2 or between 0 and 1. 23 bits in 32 bit floating point, 52 bits in 64 bit
    - `E = exponent` is a positive or negative number that weights the power of 2. 8 bits in 32 bit floating point, 11 bits in 64 bit.

- 3 cases on how the bit pattern is represented depending on the value of exp.
    - `normalized values` - either when exponent is neither all zeroes nor all ones (not 0 and not 255 in binary form). This is the most common case and in this case it's interpreted as representing a signed integer in `biased` for (E = e - Bias where e is an unsigned number and bias is a bias value equal to 2^(k-1) - 1 which gives 127 for single precision and 1023 for double). This gives ranges of -126 +127 for single precision and -1022 +1023 for double
        - mantissa / significand is interpretend as a binary fractional value between 0 and 1 and adding 1 to that fractional value. That added 1 is called an `implied leading 1` because that 1 is not actually stored. It's a trick to get an extra bit of precision for free since you can adjust that frac value to give M in the range of 1->2 instead of just having values in the range of 0->1. This doesn't fully make sense to me but exercises will probably help
    - `denormalized values` - when the exponent is all 0s. Exponent is 1 - Bias so the bias is 1 in this case. Mantissa / significand is just the fractional value, no implied leading 1. The sign bit can still either be 0 or 1 which leads to an interesting case where there can be + or - 0.0, which are treated sometimes as the same and sometimes different.
        - denormalized values let you represent 0 in floating point, and also to represent numbers that are very close to 0. It also gives something called `gradual underflow` where numberic values are evenly spaced near 0.0
    - `Special values` - when the exonent is all 1s. If the fractional field is all zeros then this represents infinity, depending on the sign either positive or negative. When the fractional value is not zero this is interpreted as NaN.
        - these special values are used differently depending on the application

- Interesting properties of IEEE format
    - Can sort numbers numerically by their binary representation using integer sorting routines if you interpret them as unsigned integers
        - negative numbers are a little more difficult since they have a leading 1 and are in descending order. But this can be overcome. There's an exercise around this
    - Floating point numbers are more densly packed around 0 and then get more sparse as you move out. Evenly spaced as you get close to 0 with denormalized values
    - converting integers to floats you can just shift k points to the right via exponent (k being the length of the binary number) and then create the binary representation of the sign, exponent, and mantissa. Exercises for this will help

#### Rounding
- Need to round since floating point has limited precision. Need to approximate real values to the closest floating point number
- 4 different rounding modes
    - Round to even (make sure the least significant digit is even) (default)
    - Round toward zero
    - Round down
    - Round up
- The first step is to try to find the closest match. But if there's a split to choose between rounding to even is the default of IEEE and of most systems
- Treat 1 as odd and 0 as even. There's no particular reason for preferring even over odd it's just a concept that helps make sure the average of values aren't affected by exclusively rounding up or down

#### Floating point arithmetic
- You would expect two floating point numbers doing arithmetic to be like round(x _op_ y) but in practice it's more complex because you have overflow and other issues to worry about
- floating point ops do share a lot of mathematical properties with regular arithmetic but are not associative or distributive
    - associative: for example `(3.14 + 1e10) - 1e10 = 0.0`. 3.14 is lost i rounding
    - distributive: `1e20*(1e20- 1e20)` gives 0.0 while `1e20*1e20-1e20*1e20` gives NaN
- they do share monotonicity and other properties
- key point here is make sure you understand the implications of floating point ops when doing them and watch out for rounding/overflow issues
- compilers are forced not to make many optimizations and be conservative with floating point ops so they can be much slower than integer ops in certain situations

#### Floating point in C
- Actually C doesn't enforce IEEE format but most machines use it
- Key point here is when casting from higher precision to lower precision there will be rounding and potentially overflow. But when converting from lower to higher you can exactly represent those values still
- from float or double to int it will always round towards zero
