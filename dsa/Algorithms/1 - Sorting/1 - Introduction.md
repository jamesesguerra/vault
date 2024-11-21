An algorithm where you have an array of `n` numbers / keys as input, and a sorted version of the array as output.

- **destructive** - doesn't use an extra array `b` to sort `a`, just overwrites array `a` with the sorted version of `a`
- **in-place** - uses O(1) space

#### Solutions to the problem
##### Permutation Sort
- take all the permutations of the array - Ω(n!)
- check if it's sorted - Ω(n)
- overall time complexity - Ω(n! * n)
- a bad way to sort

##### Selection Sort
- for each element in the array take the largest - O(n)
- put it in the end - O(n)
- overall time complexity - O(n^2)

##### Insertion Sort
- for each element in the array - O(n)
- compare it with each element to its left and swap it if its less than it, do this until you reach the end - O(n)
- in place sorting
```python
def insertion_sort(arr):
	i = 0
	
	while i < len(arr):
		j = i - 1
	
		while j >= 0 and arr[j] > arr[j + 1]:
			arr[j], arr[j + 1] = arr[j + 1], arr[j]
			j -=1

		i += 1

return arr
```