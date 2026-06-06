# Data structures and algorithms

Tables below are useful quick reference.

---

## Hashing

- hashing functions are cool. Dictionaries use them to basically for a given input return the index of where the value is which makes it o1

## Sorting algorithms
- `bubble sort` - just swaps values locally with a bunch of passes over the array. Slow but simple
- `merge sort` - recursively splits data into halfs and then sorts them by merging them back together. Faster but uses more memory w/ recursion
- `insertion sort` - loops through the array once and as it finds elements that are less than the one that came before, it moves it back until those are no longer the case. So because of all the swapping and checking it's O n^2 but is actually faster than merge sort for small arrays due to smaller constants. Also fast for partially or mostly sorted lists. Constant memory
- `quick sort` - recursively partions the array and each recursion puts the partition value in the right place with the values smaller than the partion to the left, and those bigger to the right. This uses less memory than merge sort and can be just as fast. But worst case performance can be n^2 if the list is already sorted. Two methods to get best performance is to randomize the list (o(n) time) or pick the partition based on a median calculated by a small sample of the data which is basically o(1) time. The median approach is usually preferred. Merge sort is more stable though

| Algorithm                       | Time Complexity (Avg / Worst) | Space Complexity               | Basic Description                                                    | When to Use                                                                           |
| ------------------------------- | ----------------------------- | ------------------------------ | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Bubble Sort**                 | O(n²) / O(n²)                 | O(1)                           | Repeatedly swaps adjacent elements until sorted.                     | Rarely used in practice; good for teaching or when list is *almost* sorted.           |
| **Insertion Sort**              | O(n²) / O(n²)                 | O(1)                           | Builds sorted portion one element at a time by inserting into place. | Great for small arrays or nearly sorted data; simple and stable.                      |
| **Selection Sort**              | O(n²) / O(n²)                 | O(1)                           | Finds the minimum each pass and puts it in place.                    | Useful if swaps are very costly but comparisons are cheap.                            |
| **Merge Sort**                  | O(n log n) / O(n log n)       | O(n)                           | Divide array into halves, sort recursively, then merge.              | Stable, predictable performance; good for linked lists or when stability is required. |
| **Quick Sort**                  | O(n log n) / O(n²)            | O(log n) avg (recursion stack) | Picks a pivot, partitions array around it, then sorts partitions.    | Very fast in practice; best general-purpose choice with random pivots.                |
| **Heap Sort**                   | O(n log n) / O(n log n)       | O(1)                           | Builds a heap, repeatedly extracts max (or min).                     | Good when you need guaranteed O(n log n) and in-place sorting.                        |
| **Counting Sort**               | O(n + k) / O(n + k)           | O(n + k)                       | Counts occurrences of values, reconstructs sorted output.            | Best for small-range integers (like ages, scores). Not comparison-based.              |
| **Radix Sort**                  | O(nk) / O(nk)                 | O(n + k)                       | Sorts by digits/characters from least significant to most.           | Great for fixed-length integers/strings; not comparison-based.                        |
| **Tim Sort** (Python’s default) | O(n log n) / O(n log n)       | O(n)                           | Hybrid of merge + insertion sort, optimized for real-world data.     | Use Python’s built-in `sort()`/`sorted()`. Handles most cases very efficiently.       |


## Data structures

- `stack` - last in first out for when you just need to access the top
- `queue` - is similarly simple to a stack, but its FIFO. You add to a tail and take from the head
- `linked list` is just a list with values and then a reference to the next value. A doubly linked list would be a reference to the last element as well
- trees
    - preorder traversal visits the parent before the children
    - postorder traversal visits the children before the parents
    - inorder traversal visits the children, then the parent, then the rest of the children. So basically in numeric order for a binary tree
    - height of trees is the longest path you can take
    - unbalanced trees can be a problem because it makes traversal worst can o(n). Trees should be as shallow as possible to avoid this
    - red black trees are an example of an auto balancing tree. rotations are the key there
- `hashmaps` `hash tables`
    - assuming you know the key, can do O(1) operations. Python dictionary is an example
    - hashmaps are usually a hashing function that maps a value to an index on an array. Most hash functions are imperfect and don't always map unique keys to unique values. When two values map to the same index that's a `hash collision`. But that's usually accounted for in practice
    - for something to be "hashable" it has to be immutable and have a consistent hash value. So like in python you can hash a tuple but not a list
    - `LINEAR PROBING` is one basic strategy to use for hash collision. Basically just check for the first empty spot after the colliding index and store a key value pair there. When you do a lookup you check and make sure the key is a match to handle the collision. If it's not a match you keep looking around it through the same process as storing the key
- `Trie`
    - This one seems like it might be useful for autocomplete or something
- `graphs`
    - all trees are graphs, but not all graphs are trees. A graph is just a set of edges and vertices

| Data Structure                    | Typical Operations (Avg / Worst)                                         | Space Complexity | Basic Description                            | When to Use                                                                                     |
| --------------------------------- | ------------------------------------------------------------------------ | ---------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Array / List**                  | Access: O(1) / O(1)<br>Search: O(n) / O(n)<br>Insert/Delete: O(n) / O(n) | O(n)             | Contiguous block of memory; direct indexing. | Best for fast random access, compact storage; not great for frequent inserts/deletes in middle. |
| **Linked List (Singly/Doubly)**   | Access: O(n) / O(n)<br>Insert/Delete (at head/tail): O(1) / O(1)         | O(n)             | Sequence of nodes with pointers.             | Good for frequent inserts/deletes; poor for indexing or cache locality.                         |
| **Stack**                         | Push/Pop: O(1) / O(1)                                                    | O(n)             | Last-In-First-Out (LIFO) structure.          | Use for undo features, expression parsing, DFS.                                                 |
| **Queue**                         | Enqueue/Dequeue: O(1) / O(1)                                             | O(n)             | First-In-First-Out (FIFO) structure.         | Use for task scheduling, BFS, buffering.                                                        |
| **Deque**                         | Insert/Delete both ends: O(1) / O(1)                                     | O(n)             | Double-ended queue.                          | Great when you need a queue and stack in one (e.g., sliding windows).                           |
| **Hash Table / Dict / Map**       | Insert/Search/Delete: O(1) avg / O(n) worst                              | O(n)             | Stores key-value pairs, uses hashing.        | Fast lookups; Python `dict` is highly optimized. Worst case rare if hash spread is good.        |
| **Set**                           | Insert/Search/Delete: O(1) avg / O(n) worst                              | O(n)             | Hash table with unique keys only.            | For membership testing and ensuring uniqueness.                                                 |
| **Binary Search Tree (BST)**      | Search/Insert/Delete: O(log n) avg / O(n) worst                          | O(n)             | Hierarchical structure; left < node < right. | Good for dynamic sorted data; balance is key.                                                   |
| **Balanced BST (AVL, Red-Black)** | Search/Insert/Delete: O(log n) / O(log n)                                | O(n)             | Self-balancing BST.                          | Guaranteed performance; widely used in libraries.                                               |
| **Heap (Binary Heap)**            | Insert: O(log n)<br>Extract-min/max: O(log n)<br>Peek: O(1)              | O(n)             | Tree-based structure for priority queues.    | Use for scheduling, shortest path (Dijkstra), “k smallest/largest” problems.                    |
| **Graph (Adjacency List)**        | Add edge: O(1)<br>Traverse: O(V+E)                                       | O(V+E)           | Collection of nodes + edges.                 | Sparse graphs; most common representation.                                                      |
| **Graph (Adjacency Matrix)**      | Add edge: O(1)<br>Traverse: O(V²)                                        | O(V²)            | 2D matrix for edges.                         | Dense graphs; quick edge lookups.                                                               |
| **Trie (Prefix Tree)**            | Insert/Search: O(k) / O(k)                                               | O(n·k)           | Tree storing strings by prefixes.            | Useful for autocomplete, spell check, word dictionaries.                                        |


## Searching
- depth first and bredth first and the big ones. Lots of algorithms out there to do both

## P vs NP
- `P` are all problems that can be solved in polynomial time
- `NP` are all problems that can be verified in polynomial time
- NP is a superset of P because many problems cannot be solved in polynomial time but can still be verified
- Traveling salesman problem is an example
- NP completeness is something that all problems in NP can be reduced to in polynomial time
- NP hard is something that all problems in in NP can be reduced to in polynomial time, but even harder because it doesn't have to be in NP