### `map`
`map` takes each value from stream `A`, applies `f()` on it, and produces a new value on stream `B`

![[rxjs-map.png]]

### `flatMap`
A version of `map` that flattens a metastream aka a stream of streams, by emitting on the trunk stream everything that will be emitted on branch streams. You can think of it as an operator that automatically subscribes to a stream value emitted by a parent stream, and emitting each value emitted on that child stream into the parent stream.

![[rxjs-flatmap.png]]

### `merge`
Use `merge` if you have multiple streams that produce values independently and you want to combine their output into a single stream

![[rxjs-merge.png]]

### `startWith`
Useful for creating a stream with an initial value; so that no matter how the stream will look like over time, if you chain `startWith(x)`, it will always have `x` at the beginning.

![[rxjs-startWith.png]]

### `combineLatest`
Takes two streams `A` and `B` as inputs, and whenever each stream emits a value, it joins the two most recently emitted values `a` and `b` from both streams and outputs a value `c = f(a, b)` where `f` is a function you define. This is useful for using cached values.
![[rxjs-combineLatest.png]]

### `switchMap`
Starts with a value from `A`, feeding that value into another stream `B` and returning that stream. Useful if you have a stream (`B`) that depends on a value from stream `A`.
![[rxjs-switchMap.png]]