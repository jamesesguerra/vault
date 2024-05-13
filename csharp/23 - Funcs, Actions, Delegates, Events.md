Methods don't just have the ability to store values and references, they can also store **methods**. 
### Funcs
Funcs allow you to abstract **actions** and not just values. So you can think of a filter test not as the specific filter e.g. `number > 10` but as a method `IsGreaterThan10`. You can store this action in a variable, and pass it to a higher-order function so it can use that method.

Without `Func`s, if you want to make filter methods, you have to create 1 for every test.

```cs
IEnumerable<int> FilterLessThan10(IEnumerable<int> list) {
	var numbersGreaterThan10 = new List<int>();
	
	foreach (var item in list) {
		if (item > 10) {
			numbersGreaterThan10.Add(item);
		}
	}

	return numbersGreaterThan10;
}

IEnumerable<int> FilterLessThan5(IEnumerable<int> list) {
	var numbersGreaterThan5 = new List<int>();
	
	foreach (var item in list) {
		if (item > 5) {
			numbersGreaterThan5.Add(item);
		}
	}

	return numbersGreaterThan5;
}
```

Funcs allow you to abstract the condition that's tested in every if-statement inside the filter since you can just think of it as a predicate that you pass an element to, and get back a boolean. That is, you can think of it as `predicate(item)` and you don't have to know what test is being done to the item, you just care about testing it if it's true, and adding the current item to the filtered list.

```cs
public static IEnumerable<T> Filter<T>(this IEnumerable<T> list, Func<T, bool> predicate) {
	var passed = new List<T>();
	
	foreach (var item in list) {
		if (predicate(item)) {
			passed.Add(item);
		}
	}
	return passed;
}
```


### Delegates
Delegates are like a container or list for `Func`s or `Action`s that conform to a certain method definition ie they have to have the same return type and parameter list.

```cs
delegate int ProcessInt(int value);
```