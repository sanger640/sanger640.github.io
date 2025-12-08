---
layout: distill
title: Data Structures and Algorithms Crash Course
date: 2025-05-02
description: 
tags: algorithms
categories: algorithms
related_posts: false

bibliography: paper.bib
---

# Data Structures

## Computer Components
- CPU: executes instructions, does calculations (in billions/sec)
  - Control unit: fetches instructions from memory and what it means
  - ALU (Arithmetic Logic Unit): does the calculations through logic gates
  - Registers: tiny storage spots (few bytes), hold numbers while they are processed
- RAM: temp storage, holds running programs
  - "Workspace"
- Storage (Disk): permanent storage, large but slower than RAM, program loads from storage to RAM and executed in CPU
- This is the "Von Neuman" Architecure
  - Store both program instructions and data in same memory (RAM)
  - Have CPU fetch and execute instructions sequentially
- CPU 100x fater than RAM, 10,000,000x faster than Disk
  - Only holds 64 bytes of memory
- **Data Structures live in RAM**

## References vs Primitives
- Values like integers, booleans are simple so are directly stored by the variable.
- Values like lists, or custom objects are stored via a reference. The variable holds a reference to the value.
  - Reference is a way to find where the data lives in memory.
  - When you copy a reference of a variable and change it's value, it will change the value for both variables as they reference is the same.


## Memory Size of Types
- Byte: 8 bits (8 ones or zeros)
- Ex:
  - Bool : 1 byte, Integer: 4 bytes. Double: 8 bytes
- Why size matters for arrays?
  - When initialziing, it needs to know how much space to allocate, different types have different needs? Array of 5 int vs 5 bool
- Address formula: base_address + (index x element_size)
  - ex: 1000 + (2*4bytes) = 1008

## Memory Alignment
- Small gaps in memory between variables to keep data aligned to specific boundaries based on their byte multiple (element size). allows for efficent reading by CPU.

## Strings are special
- can be variable in length
- Usually stored as 3 components:
  - pointers (8 bytes) that reference the actual character data.
  - Char data store elsewhere in memory
  - length info to track how many characters exist

## Stack and Heap
- Computers organize memory of a program in two main regions:

### Stack
- stack: fast, organized region for temp data that follows strict rules (LIFO)
- only add or remove from top
  - when function is called a new "frame" is added to the stack containing:
    - function params
    - local variables
    - return address (where to go back after the function finishes)
  - when the function returns, its frame is removed from the stack
  - extremely fast
- stack only few MBs
- stack stores primitive values like int and bool directly, complex types like objects and arrays are stored elsewhere with only their references living on the stack.

### Heap 
- heap: flexible region for data that need to live longer or grow dynamically
- "large warehouse"
  - store items of any size for as long as you need
  - objects arrays that grow dynamically stay on the heap
- slower than stack access, still fast
- can be GBs in size
- large datastructures are here
- Heap memory requires explicit management
  - C++ needs manual allocation and freeing of heap memory
  - Python uses garbage collection (automatic)
- Stack references point to heap objects
  - reference (pointer) lives on a stack, actual data lives on heap

### How it helps in coding?
- Don't make too many nested function calls, or allocating large local arrays can cause stack overflow errors.
- Recursive functions that never terminate are a common cause of this problem
- Stack operations: fast bc allocation and deallocation simple move a pointer
- Heap operations: search for available sapce, tracking allocations, adding overhead.
- When performance matters, keep frequently accessed data on stack is very useful.
- Memory leaks happen on heap when allocation objects are never freed.

## References vs Values (Mutation problem)
- reference types share data
- when you assign one var to another, you copy the refernce, no the entire object. Both variables now point to the same object in memory.
  - if you change the value via one variable the other will change to as they refer to the same object.
  - common source of bugs
- happens when passing objects to functions
  - function recieves copy of the reference (pointing to same object)
  - function mutates the object, the caller sees the change
  - "pass by reference" behavior
- If you do not want the passed value to change, "copy"
  - true indepent copy without affecting the original
  - deep copies that duplicate the object itself not just the reference.
    - deep copies make sure event nested objects are truly independent copies
- In C++, can pass by value (copy), by reference (with &), or by a pointer.
- "reference is an immutable pointer which cannot be null"
- References are much faster than copying the entire array
  - but make sure of mutations

## Why Data Structures?
- different way of organizing data makes search and insertion operations faster
- how you organize data determines how fast you can work with it
- linear search on an unsorted array (O(n)) (1000 searches worst case) vs binary search on a sorted array (O(log(n))) (10 searches worst case).
- O(log(n)): want this as much as possible as growth is very slow even when n explodes.
- By imposing structure on data, you can enable a faster algorithm.
- Arrays good for accessing, linked list good for insertion, hastable good for checking memberships, stacks and queues enforce specific orderings.

## Matching structure to problem
At scale, performance determines if software is usable
- Array: fast index access O(1); slow middle insert O(n)
- Hash Table: fast lookup O(1); fast insert/delete O(1)
- Linked List: Fast head insert O(1), slow access O(n)
- Stack: LIFO order, used for undo, backtracking
- Queue: FIFO order, used for scheduling and BFS
- Trees: fast sorted operations O(log n); Hiearchical data, DFS

# Algorithmic Complexity

## Counting operations
- Operations: assign, reading, arithmetic, comparisons, array access, function calls
  - each are a form of opeartion
- Loops multiply Operations
- Nested loops multiply operations across all levels
- Counting predicts performance before running code.
- Counting based on input size the most important

## Constant vs Linear Time
- Constant: operations stay same despite input size 
  - **O(1)**
  - Ex: array access always takes 1 operation
  - address = base_address + (index × element_size)
  - Other ex: var assignment, array update at indec, simple arith, compariosn,get vector size, vector push_back
  - Ideal
- Linear: Operations grow proportionally with input size
  - **O(n)**
  - Ex: searching an unsorted array (linear search), summ all elements, find max, count occurrence
  - Acceptable for most tasks
- **Recognizing Constant Ops:** No loops, recursion. Direct access.
- **Recognizing Linear Ops:** Single loop through array/list, visiting every element once, searching unsorted data, summing/counting or processing all elements.
  - Constant factors don't change the category; two seperate for loops are stil O(n)
  - We care about growth rate, not exact counts.

## Quadratic Time
- **$O(n^2)$**
- In presence of nested loops:
  - Ex: comparing all pairs in an array (for distance, collision, other prop); generating multiplication table
  - Gets slow quickly, okay for very small input sizes; fail catstropically with high input sizes.

### Avoiding Quadratic Time
- Use better data structures: hash tables for O(1) lookup, instead of O(n) search. For dupes detection; use a set instead of nested loops.
- Sort first, then use efficent algorithm
  - Sorting is O(n log n), which is much better than O(n^2)
  - After sorting, many problems become O(n)
- Use two-pointer techniques
  - Sometimes, two pointers moving through a sorted array can replace nested loops
- Look for math patterns

## Best, worst, and average case
- Best case: luckiest input, min possible operations.
  - For linear search, first position is the target O(1)
- Worst case: max possible operations
  - Lin search. O(n)
- Average Case: expected performance
  - Lin Search: (n+1)/2 approx. n/2 (half of the worst case); still linear growth O(n)
- Focus on worst case:
  - Gurantees matter
  - Often common
  - Average case can be hard to define: real world data distribution are rarely uniform
  - Best case is usually trivial
- Ex: finding minimum has same O(n) for best, avg, and worst.
  - Have to go through all elements

## Space Complexity:
- Previous vs time complexity: how many operations an algorithm performs
- Algo also takes memory
- Count how much memory an algo needs relative to its input space
- O(1): constant space; count, sum
  - "in space" algorithms; modify existing data structures than creating new ones
- O(n): linear space; new array of size n
    - Auxillary arrays; create new data structs proportial to input size; needed to store interm results or build output without modifying the input.
- O(n^2): quadratic space; matrix n x n
  - matricies; common in problems involving grids, graphs, or pairwise comps.
- Space comp: how much add. memory an algo needs beyond itself

### Space-Time trade offs
- Can trade memory for speed, or vice versa
  - Minimal space, slower
    - store only last few values
  - More space, faster lookups
    - store all computed vals in array
- Depends on constrants: limited memory vs fast repeacted access

### When space matters:
- Large datasets
- Embedded systems: limited RAM
- Server apps
- Mobile apps

# Arrays:
- Collection of elements stored in contiguous memory locations; meaning they sit side by side in the computer's memory
- Elements stored sequentially (4 bytes aprt for 32 bit int)
- Zero-based indexing: first element at index 0
  - Address calculation: address = base + (index x element_size)
- **O(1) direct access**, key insight
- CPU cache for nearby data loaded
- Reading and updating are O(1) constant time operations.
  
## Iteration
- Linear serach (linear iteration, for loop)
  - start at i = 0, check iterate until found
  - -1 if none
- Visit each element one by one
- O(n) linear time operation
- Tasks: summing, searching
- Array not sorted so can't skip

## Adding/Removing elements at the end
- Adding to end, append/push O(1) operation
  - nothing to move, update length
- Common pattern is to start with empty array and build it by appending
  - Each append is O(1)
  - no need to specify size
  - total time for n appends, O(n)
- Removing from end: called pop
  - O(1), no element to shift


## Adding/Removing from Middle
- Much slower: elements must shift
- Middle operations O(n):
  - Insert/Delete: must shift elements right (insert), left (delete)
- Insert (shift right):
  - start from end and work back
  - shift each by one to right
  - continue till i, and place new element at i
  - update arrays length
  - n-i number of shifts
- Delete (shift left):
  - remove element at i
  - shift each element after i one pose to left
  - continue until end 
  - update array's length
  - n-i-1 (number of shifts)
- Alternative for deletions:
  - **swap with last element, then pop**
- if freq insert/delete, use linked list
- to maintain order, use end of array 

## Dynamic Arrays
- Trad arrays need a specified size, if that size is reached, you must allocate a new array and copy everything over manulaly
- Modern languages have dynamic arrays:
  - List in python
  - Vector in C++
- Dyn Array maintians two seperate value: size (current numer of ele) and capacity (number of slots allocated in memory).
  - Empty dyn array will allocate 4 ele cap
  - When reach cap, it will choose a memory block with double cap and copy everything over.
  - **Doubling capacity each time**
  - 4->8->16->32->64
  - Why doubling each time?
    - Amortized
    - Copying takes O(n) time
    - If resize every append, n elements require O(n^2) ops.
    - With doubling, resize happens at power of 2.
    - For n appends, total copy work is O(n). Total append work are O(n), fiving O(n) total time for n operations.
    - Divide by n and you get O(1) per operation on average.
    - This is called **amortized O(1)** time.
    - W**hile a single append might trigger an expensive O(n) resize, most appends take O(1). Averaged out, each append costs O(1).**
- **Memory tradeoff:** use more memory than strictly necessary.Right after resize you are at 50% full.
  - For most apps, acceptable. But for memory efficency switch to trad arrays if needed.

# Strings
- Array of chars
- Strings live in the heap (like arrays)
- When var created, that var holds a reference pointing to the actual char data stored in heap memory.

## Indexing
- Zero Indexing
- Works just like array indexing

## Iteration
- used for counting chars, validate format, transform, search patterns.
- same as array iteration

## Concactenation
- join mulltiple strings in one
- '%+' opearator to concatenate
- or use append()
- strings are immutable, concatenating creates new string object each time.
- .reserve(num_elements) to avoid relocation when appending.

## Comparison
- equality test: same chars in same sequence
- lexigraphical comparioson (position based)

## String Methods
- .find("phrase");
- substr(i1, i2);
- .replace(string_arr, index, phrase);
- .length(); .size(); .empty();

## Immutability
- strings are immutable: cannot be changed after they are created.
- modification creates a new string instead
- Python enforces string immutablity
- C++ allows mutable strings. std::string supports direct char assignment
  - make copy if you want to preserve

## Character Counting
- Uses hash maps (or arrays) to track how many times each char appears in a string.
  - Iterate through string once, update counts as you go
  - Create a map from char to frequency
  - Use unordered maps
- Used in anagram detection (compares two freq maps for equality)
  - Finding dupes if any count exceeds one
  - Char validation verifies req chars are there
  - Pattern matching
- Transforms strings into struct data for analyis
  - iterate once through string
- O(n) time to build a map
  - O(1) lookups for each char frequency
  
# Linked List
- To insert in an array at certain pose, need to move everythin to the right of it by 1. Max n operations
- Linked Lists scatter elements throughout memory and connect them through pointers
  - No continuous blocks
  - Easy to insert O(1)
- Linked List is made of:
  - Nodes: contains the data and the pointer to next node
  - Must follow the chain to iterate, cannot jump
  - Starts with head pointer (points to first node), each node points to next node in sequnce.
  - Last node points to nothing (represented as null pointer), marks end of the list.
- **Inserting:** create new node, point it to the node after it, change previous node's pointer to point to the new node. Same for deletion. O(1)
- **Accessing:** Linked list requires traversal to access at an index unlike array. O(N) complexity for Linked List for accessing.
- Array optimize for access speed, linked list optimize for modification flexibility.
  
## Nodes and Pointers
- Node is a simple struct of data and pointer
  
```cpp
struct Node {
    int data;
    Node* next;

    Node(int data) : data(data), next(nullptr) {}
};

// Create a node with data = 5
Node* node = new Node(5);
cout << node->data << endl;  // 5
cout << node->next << endl;  // 0 (nullptr)

// Create two nodes
Node* first = new Node(10);
Node* second = new Node(20);

// Link first to second
first->next = second;

// Now: first -> second -> nullptr
cout << first->data << endl;         // 10
cout << first->next->data << endl;   // 20
cout << second->next << endl;        // 0 (nullptr)
```
## Traverse O(N)
```cpp
void traverse(Node* head) {
    Node* current = head;
    while (current != nullptr) {
        cout << current->data << endl;
        current = current->next;
    }
}
```
- Binary search does not work on LinkList

## Insert
- At head easy O(1), at tail have to traverse the entire list; if no tail pointer.
  - With tail pointer O(1)
  - Implementations like queues: maintain a tail pointer.

## Deletion
- Skip over unwanted nodes, disconnect from chain. change the pointer of the previous node to the next node.
- C++ requires explicit memory deallocation (delete head)
- Edge cases matter: empty list (nothing to delete), single node then return null, target not found, return original head, multiple occurance, do only first one (or remove early return).
- O(n) for delete by val, search dominates complexity
- O(1) delete at head

## Linked List vs Array
- Arrays for jumping around random positions (binary search, mat ops)
- Insert at begin: linked list O(1)
  - Stack or undo ops
- Insert at end: arrays with extra cap win O(1) dyn array, linked list need a tail pointer else O(n)
- Insert middle: both O(n), traversal kills linkedlist, inserting by itself is O(1).
- Deletion (mirrors insertion)
- Both pretty much O(n), for value based
- Search: O(n) for unsorted data, arrays can be sorted for bin search O(log n), sorted ll are still O(n)
- Mem Usage: array is contig (heavy stack memory but faster access due to RAM cache), ll is scatt mem easier on memory (slower RAM access,)
  - sequential travel array faster (even tho both O(n)) due to RAM cache or overhead
- Linked list good for complex structures: trees, graphs, node based designs