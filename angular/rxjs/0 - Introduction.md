#### Links
- https://gist.github.com/staltz/868e7e9bc2a7b8c1f754
- https://www.learnrxjs.io/learn-rxjs/concepts/rxjs-primer

**Reactive programming**
- is a new way of building apps where your app will react to changes and handle data streams rather than having to write each handler separately
- letting go of imperative and stateful habits of typical programming

### React vs Angular with RxJS
In React:
- you fetch data (possibly) in `useEffect`, store state with `useState` and re-render when the state changes
- fetch data -> store state -> re-render
- you manually trigger updates to state
- **promises:** are one and done; runs once and completes
- **problem:** you have to manage the state manually ie if you need to debounce, transform, or cancel the data in some way, you have to do it yourself

In RxJS:
- the data stream flows continuously as a stream
- you transform it before it reaches your component
- because it structures data as a stream, you don't need separate event listeners 
- **observables:** a stream of continuous values over time
- there are built-in operators like `map`, `filter`, `switchMap` that transform the data in real-time


**RxJS** is a library that helps us making asynchronous calls and implement callback mechanisms easily without callbacks

It contains 3 main things that helps us update our data streams
- Observables
- Satellite Types - Observers, Schedulers, Subjects
- Operators - array methods (map, filter)

### Observable
An **observable** is a continuous data stream i.e. data that's continuously changing e.g. an order status. 

### Subscriber
A **subscriber** listens to an Observable for data changes / updates