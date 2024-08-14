
### Reactive Programming
Reactive programming is **programming with asynchronous data streams**. A data stream in this case is just a series of values that are emitted **over time**. It can be anything, from a browser event, to primitives like numbers and strings.

These streams are called **observables** which you can listen to by defining functions called **observers** which can be run when (1) a new value is emitted, (2) the stream is completed, and (3) an error was transmitted. You can also pipe these streams into methods that transform them in some way, but don't affect the original stream, something called **immutability**. 

```
----a----b---c-----d---e--f-g----X---X---|--->

			     TIME
```


#### Solving problems with reactive programming
The RxJS mantra is that (almost) everything can be a data stream. You can model whatever you need in your software requirements to be a stream of *something*.

**example:** 

```ts
var requestStream = of("https://api.github.com/users");

// create an observable that emits one url
// ----- https://api.github.com/users ------>
```

Once the event is fired, that tells you *when* to make the request and *what* URL to use.
```ts
requestStream.subscribe({
	next: requestUrl => {
	}
})
```

Making an HTTP request is also an asynchronous operation with `fetch`, and so you can wrap the call to `fetch` with a custom observable ie a stream of the users in JSON format
```ts
requestStream.subscribe({
	next: requestUrl => {
		var responseStream = Observable.create((observer: Observer) => {
			fetch(requestUrl)
				.then(raw => raw.json())
				.then(user => observer.next(user))
				.catch(err => observer.error(err))
				.finally(() => observer.complete());
		})

		// you can then 'listen' to the list of users in JSON and log them to the console
		// --- user1 --- user2 --- user3 --- user4 --->
		responseStream.subscribe({
			next: (user: User) => {
				console.log(user);
			}
		})
	}
})
```
