**The goals:**
- solve computational problems
- to prove the correctness of our solutions
- to argue about the efficiency of our solutions

#### Computational problems
A computational problem is the problem of mapping a set of inputs to their corresponding correct outputs. We want to be able to test each input against the problem's predicate and know for which inputs is it true

#### Algorithms
An algorithm is a series of steps that given an input, will get you the correct output. They are like mathematical functions in this sense where you can plug in an input to a function and receive an output (`fn(input) -> output`). They're supposed to work for general problems i.e, the general problem of sorting as opposed to sorting a list of numbers, and are supposed to work for arbitrarily-sized inputs.

---
##### Proving Correctness
Proving the correctness of algorithms is difficult because even if it seems like the algorithm is correct at solving the problem at first, there might be inputs for which it will fail. Therefore, you need to use **mathematical induction** (recursion for computer scientists) to prove its correctness.

##### Proving Efficiency
How can we prove the efficiency of our algorithms? We can time it, but it wouldn't be accurate since computers have varying capabilities. A fair way to do it is to count the number of fundamental operations our algorithms perform to solve the problem.

**Big O**
- we expect the number of operations our algorithms perform to depend on the size of the input
- we use asymptotic functions to relate how the number of operations grow as the size of the input grows as well
- Big O is the worst case or upper bound, and omega is the best case or lower bound

**What are fundamental operations**
We use a word-RAM model of computation. The CPU addresses fixed-size words from RAM. For a 32-bit architecture, words are 32-bits, and so the number of memory addresses the CPU is limited to addressing is 2^32 or ~4GB. Once the CPU reads words from memory, it can perform binary operations on them such as:
- logical operations
- arithmetic operations
- bitwise operations
- read / write operations

**Data structures** then store, organize, and operate on those data.