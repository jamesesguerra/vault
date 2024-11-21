#### Resources
- https://www.youtube.com/watch?v=-gjxg6Pln50
- https://www.youtube.com/watch?v=1pkOgXD63yU

The **two pointers** technique is a generic version of the sliding window in that you have two variables referencing indices in a sequence, except in this technique, the two pointers can cross each other can can be on different sequences. 

#### Problems
- [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/description/)
- [Sort Colors](https://leetcode.com/problems/sort-colors/description/)
- [Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/description/)
- [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)
- [3Sum](https://leetcode.com/problems/3sum/description/)
- [Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)
- [Container with Most Water](https://leetcode.com/problems/container-with-most-water/description/)

#### Sliding window vs 2 pointers
- **sliding window** - you're trying to find a certain subarray / subsequence
- **2 pointers** - you're interested in comparing / finding pairs of elements 

#### Recognizing in the wild
- you have 2 sequences to process
- you're trying to maximize something as well, except maximizing it doesn't always mean moving the pointers in the same direction e.g. container with most water

#### Common Patterns
**2 pointers, each starting from the beginning and end until they both meet** - Sorted Two Sum
Since the input array is sorted, if the sum of the two pointers isn't enough, you move the first pointer forward to get a higher value. if its too large, you move the second pointer backwards to get a smaller value.
```python
def twoSum(target, nums):
	first = 0
	second = len(nums) - 1
	
	while first < second:
		sum = nums[first] + nums[second]
		
		if sum == target:
			return [first, second]
		elif sum < target:
			first += 1
		else:
			second -= 1
```

**1 pointer moving slow, while the other moves at twice the speed** - Detecting a cycle in a linked-list
Since you have 1 pointer moving at twice the speed of the other one, they're never bound to be referencing the same node unless there's a cycle. If by the end of the loop they never meet, there's no cycle


**Processing two arrays** - Merge Sorted Array
When given two arrays, it's common to have a pointer in each array to traverse/compare both of them, while incrementing / decrementing one when needed. 

When dealing with pointers:
- the last index of an array is `len(array) - 1`
- the window / subarray size between two pointers is `(end - start) + 1`
- when dealing with looping from the back, if `end == len(arr)` the condition will be `while end > 0` and accessing the elements will be `arr[end - 1]`
- if you want to loop from the start til the end, start from 0 and the looping condition is `while i < len(arr)`
