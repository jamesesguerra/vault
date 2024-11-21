#### Resources
- https://www.techinterviewhandbook.org/algorithms/array/
- https://www.youtube.com/watch?v=CHhwJjR0mZA&list=PLUl4u3cNGP63EdVPNLG3ToM6LaEUuStEY&index=2
- https://neetcode.io/courses/dsa-for-beginners/1

### ADT vs Data Structures

| ADT                              | Data Structure                              |
| -------------------------------- | ------------------------------------------- |
| specification                    | representation                              |
| what data can be stored          | how data is stored                          |
| what operations can be performed | how operations are performed aka algorithms |
| problem statement                | solution                                    |

#### 2 main interfaces
These interfaces store `n` things, and we care about their values, and sometimes, their order
- **sets**
- **sequences**

##### Static sequences (ADT) - Static arrays (DS)
Static sequences have a constraint that you can only store `n` things
- `build(X)` - build a sequence with `n` items from `X`
- `len()` - return `n`
- `iter_seq()` - return each item of the sequence as output
- `get_at(i)` - gets the ith element of the sequence
- `set_at(i)` - sets the ith element of the sequence

The operations `get_at(i)` and `set_at(i)` take constant time. To see why, we can look back at our word-RAM model of computation. An array is just a continuous block of memory. Memory is an array of `w`-bit words. This means that to get to the ith element of an array, we simply need to index the w-bit word memory with the address of the start of the array + i. That is:

```
array[i] == memory[start_addr(array) + i]
```

And we can assume this as long as `w` (the size of the word) is greater than or equal to `n` (the size of the RAM). If this isn't true, we won't have enough numbers to address all the blocks of memory in RAM.

##### Dynamic sequences (ADT) - Dynamic arrays (DS) 
Dynamic sequences have all the methods of static sequences +
- `insert_at(i)`
- `delete_at(i)`

#### Data Structures
##### Linked Lists

##### Dynamic Arrays
Relaxes the constraint of static arrays that `len()` must be `n`
