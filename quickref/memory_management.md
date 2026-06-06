# Memory management

## C stuff

### Pointers and arrays

- arrays are basically pointers. But sometimes they are treated as actually the full array of values, like when you do sizeof. But when you pass to functions and do other ops it just passes an address to the first element. If you do sizeof in the main loop vs sizeof in a function you'll see the sizes are different due to this decay

### Structs

- ordering items in structs from smallest to largest generally minimizes padding and can help make your code more memory-efficient and performant
- structs referencing themselves need to be forward declared by using signatures before, or for circular references can forward declare

### Enums

- just a fixed set of values

### Unions

- these a sum type that can hold more than one type of thing. But not at the same time. So like you can have a union of int, char, etc. but if you set one then that's the only one that has a value. The rest is garbage/overwritten. And you can overwrite w/ any of those values in the future. So kind of like dynamic typing
- size is the size of the largest field, so be careful here if you only plan to use small union fields most of the time

---

## Stacks and heaps

### Stack

- `stack` is where local variables are stored. Has a LIFO data structure of memory frames. Each time a function is called a new stack frame is made, and when that function is over then it's deallocated
- stack is much faster and simpler than heap. allocation is just increment/decrement of a pointer
- stack memory is contiguous and cache-friendly
- stack is basically auto managed w/ function calls
- stack is thread safe because each thread has its own stack
- stack has limited size though, and can overflow
- don't return a pointer to stack memory in a function

### Heap

- `heap` is long lived memory over the life of a program. Dynamic memory, can be allocated or deallocated as needed
- need to malloc and free to use this
- if you don't free properly this is what causes memory leaks

---

## Generics

- this is just void pointers in C

---

## Garbage Collector

- This is automated memory management. Boosts performance if you don't have this running (C doesn't really have one for example)
- A basic implementation is just to track objects and how they're being referenced, decrementing and incrementing those counts as code runs. If it gets to 0 then you can clean up
- Need to be careful not just to remove top level objects, need to do a "deep delete"
- cyclic references can be an issue if you use basic increment/decrement. `Mark and Sweep` is an algorithm to deal with this
    - Mark Phase: Traverses the object graph, marking all reachable objects.
    - Sweep Phase: Scan memory, collecting all unmarked objects, which are considered garbage.
