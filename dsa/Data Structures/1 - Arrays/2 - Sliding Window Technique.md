#### Resources
- https://www.youtube.com/watch?v=MK-NZ4hN7rs

The **sliding window** technique is used for problems where you need to find the most optimal value for a subarray. It's used to turn a brute force algorithm O(n^2) to a O(n) operation. It does this by skipping the elements you've already looked at.

#### Problems
- [Longest Substring w/o Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)
- [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/description/)
- [Minimum Window String](https://leetcode.com/problems/minimum-window-substring/description/)

#### How it works
The technique is that you have 2 pointers that move in the same direction, and will never overtake each other. For every time you slide the window, you calculate something and ask yourself the question: *"Can I do better than the current best?"*. The 2 kinds are **fixed** and **dynamic**. In a fixed sliding window, the window doesn't get smaller / larger. In a dynamic one, the size can change as the pointers move. 

#### Recognizing in the wild
You can recognize sliding window problems by questions that have:
- min / max
- shortest / longest
- is something is contained within the sequence

#### Question variants

**Fixed variant** - max sum subarray of size `k`
In fixed variants, you don't care about resizing the window. You just care about computing a total running amount of something, and changing it as you slide the window. You then update the best value you have whenever you pass the constraint.

```python
def findMaxSumSubarray(array, k):
	current_running_sum = 0
	maxValue = -sys.maxsize - 1

	for i in range(len(array)):
		current_running_sum += array[i]
		
		if i >= k - 1:
			maxValue = max(current_running_sum, maxValue)
			current_running_sum -= array[i - (k - 1)]
	return maxValue
```

**Dynamic variant** - smallest sum >= to some value `s`
In dynamic variants, you care about the size of the window along with the current running amount and the best. If you pass the constraint, you resize the window according to however you're optimizing it. If you fail the constraint, you resize it the opposite of the way you resized it when you passed the constraint.

It's important to remember that when you pass the constraint, you wanna record the possible best value before trying to do better.

```python
def smallestSubarrayWithGivenSum(array, target):
	current_sum = 0
	window_start = 0
	smallest = sys.maxsize

	for i in range(len(array)):
		current_sum += array[i]
		
		while (current_sum >= target):
			smallest = min(smallest, (i - window_start) + 1)
			current_sum -= array[window_start]
			window_start += 1
			
	return smallest
```

**dynamic variant + auxiliary data structure** - longest substring with no more than `k` characters; in these cases, you need to track something e.g. the number of times some character has already appeared

```python
def longestSubarrayLengthWithKDistinctCharacters(array, k):
	char_map = {}
	longest = -sys.maxsize - 1
	window_start = 0

	for i in range(len(array)):
		char_map[array[i]] = char_map.get(array[i], 0) + 1

		while len(char_map) > k:
			char_map[array[window_start]] -= 1
			
			if char_map[array[window_start]] == 0:
				del char_map[array[window_start]]
		
			window_start += 1

		longest = max(longest, (i - window_start) + 1)

	return longest
```

- **string permutations** - contains?

What they have in common?
- everything is grouped in a sequence
- longest / shortest / max / min
- you have (1) something that you're trying to optimize for, and (2) some constraint