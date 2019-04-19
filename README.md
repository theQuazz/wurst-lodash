# Lodash

Functional Programming Utilities for [WurstScript](https://wurstlang.org/) inspired by [Lodash](https://lodash.com/docs/)

[![CircleCI](https://circleci.com/gh/theQuazz/lodash.svg?style=svg)](https://circleci.com/gh/theQuazz/lodash)

## Package `Lodash`

This library provides utility functions for operating on `LinkedList`
and `IterableMap` objects as well as utility classes `Range` and `Pair`.
These functions will attempt to destroy the lists and maps
that they act upon unless they are explicitly owned. To own a list or map
cast it to the `OwnedLinkedList` or `OwnedIterableMap` equivalent. To own
a closure call the `.own` method on it. There are several convenience
methods for owning closures of various arities, for example:

```
let myOwnedFunc = Function<int, string>.owned(x -> x.toString())
```

### `Range`

```
class Range
```

Represents a range of numbers. Can be iterated. Default `min` is `0`, default max is `INT_MAX`, default `incr` is `1`.

### `range`

```
function range(int min, int max, int incr) returns Range

function range(int min, int max) returns Range

function range(int max) returns Range

function range() returns Range
```

Creates a range which is iterable from start to finish by incr

### `rangeStep`

```
function rangeStep(int max, int incr) returns Range
```

Creates a range which is iterable from 0 to finish by incr

### `Pair`

```
class Pair<A, B>
```

A tuple which can be cast to index for use in data types

### `pair`

```
function pair<A, B>(A a, B b) returns Pair<A, B>
```

Creates a tuple

## Package `LodashExtensions`

This package provides various extension methods for `LinkedList` and `IterableMap`. Similar to the WurstFP API, these methods will attempt to
destroy the closures and objects they are invoked upon and passed.

### `equals`

```
LinkedList<T>.equals<T>(LinkedList<T> b) returns bool

IterableMap<K, V>.equals<K, V>(IterableMap<K, V> b) returns bool
```

Checks for equality of two lists/maps

### `takeWhile`

```
LinkedList<T>.takeWhile<T>(TriFunction<T, int, LinkedList<T>, bool> predicate) returns LinkedList<T>

LinkedList<T>.takeWhile<T>(BiFunction<T, int, bool> predicate) returns LinkedList<T>

LinkedList<T>.takeWhile<T>(Predicate<T> predicate) returns LinkedList<T>
```

Creates a slice of list with elements taken from the beginning. Elements are taken until predicate returns falsey.
The predicate is invoked with one to three argument(s): (value, index, list).

### `take`

```
LinkedList<T>.take<T>(int numElems) returns LinkedList<T>
```

Creates a slice of list with numElems elements taken from the beginning.

### `foldl`

```
LinkedList<T>.foldl<Q, T>(Q startValue, BiFunction<T, Q, Q> transform) returns Q
```

Reduces collection to a value which is the accumulated result of running each element in collection thru iteratee,
where each successive invocation is supplied the return value of the previous. The order if iteration is from left
to right. The transform is invoked with two arguments: (accumulator, value)

### `foldr`

```
LinkedList<T>.foldr<Q, T>(Q startValue, BiFunction<T, Q, Q> transform) returns Q
```

Reduces collection to a value which is the accumulated result of running each element in collection thru iteratee,
where each successive invocation is supplied the return value of the previous. The order if iteration is from right
to left. The transform is invoked with two arguments: (accumulator, value)

### `every`

```
LinkedList<T>.every<T>(Predicate<T> predicate) returns bool
```

Checks if predicate returns truthy for all elements of collection. Iteration is stopped once predicate returns falsey.
The predicate is invoked with one argument: (value).


### `any`

```
LinkedList<T>.any<T>(Predicate<T> predicate) returns bool
```

Checks if predicate returns truthy for any element of collection. Iteration is stopped once predicate returns truthy.
The predicate is invoked with one argument: (value).

### `keys`

```
IterableMap<T, Q>.keys<T, Q>() returns LinkedList<T>
```

Creates a list of the keys of map

### `values`

```
IterableMap<T, Q>.values<T, Q>() returns LinkedList<Q>
```

Creates a list of the values of map

### `map`

```
LinkedList<T>.map<T, Q>(Function<T, Q> transform) returns LinkedList<Q>

LinkedList<T>.map<T, Q>(BiFunction<T, int, Q> transform) returns LinkedList<Q>

IterableMap<S, T>.map<S, T, R>(BiFunction<S, T, R> transform) returns LinkedList<R>
```

Creates a list of values by running each element in collection thru transform. The
transform for lists is invoked with one or two argument(s): (value, index).
The transform for maps is invoked with two arguments: (key, value).

### `flatten`

```
LinkedList<LinkedList<T>>.flatten<T>() returns LinkedList<T>
```

Flattens list a level less deep.

### `drop`

```
LinkedList<T>.drop<T>(int numElems) returns LinkedList<T>
```

Creates a slice of list with numELems elements dropped from the beginning.

### `filter`

```
LinkedList<T>.reject<T>(Predicate<T> filter) returns LinkedList<T>
```

Iterates over elements of list, returning a list of all elements predicate returns truthy for.
The predicate is invoked with one argument: (value).

Alias of `filter`

### `sum`

```
LinkedList<int>.sum() returns int

LinkedList<real>.sum() returns real
```

Computes the sum of the values in list.

### `length`

```
LinkedList<T>.length<T>() returns int
```

Gets the length of list and maybe frees it

### `each`

```
LinkedList<T>.each<T>(VoidFunction<T> func)

LinkedList<T>.each<T>(VoidBiFunction<T, int> func)
```

Iterates over elements of collection and invokes iteratee for each element.
The iteratee is invoked with one or two argument(s): (value, index).

### `zipObject`

```
LinkedList<A>.zipObject<A, B>(LinkedList<B> lst) returns IterableMap<A, B>
```

Creates an IterableMap with the keys from A and the values from B

### `uniq`

```
LinkedList<T>.uniq<T>() returns LinkedList<T>
```

Creates a duplicate-free version of a list in which only the first occurrence
of each element is kept. The order of result values is determined by the order
they occur in the list.

### `uniqBy`

```
LinkedList<T>.uniqBy<T, R>(Function<T, R> func) returns LinkedList<T>
```

Creates a duplicate-free version of a list in which only the first occurrence
of each element is kept as compared by func. The order of result values is
determined by the order they occur in the list. The iteratee is invoked with
one argument: (value)

### `union`

```
LinkedList<T>.union<T>(LinkedList<T> lst) returns LinkedList<T>
```

Creates a list of unique values, in order, from the given lists

### `intersection`

```
LinkedList<T>.intersection<T>(LinkedList<T> lst) returns LinkedList<T>
```

Creates an array of unique values that are included in both given lists.

### `difference`

```
LinkedList<T>.difference<T>(LinkedList<T> lst) returns LinkedList<T>
```

Creates an array of array values not included in the other given lists.

### `indexBy`

```
LinkedList<T>.indexBy<T, R>(Function<T, R> idx) returns IterableMap<R, T>
```

Creates an iterable map composed of keys generated from the results of running each element of collection thru iteratee.
The corresponding value of each key is the last element responsible for generating the key. The iteratee is invoked with
one argument: (value).

### `groupBy`

```
LinkedList<T>.groupBy<T, R>(Function<T, R> idx) returns IterableMap<R, LinkedList<T>>
```

Creates an iterable map composed of keys generated from the results of running each element of collection thru iteratee.
The order of grouped values is determined by the order they occur in collection. The corresponding value of each key is
a list of elements responsible for generating the key. The iteratee is invoked with one argument: (value).

### `mapValues`

```
IterableMap<S, T>.mapValues<S, T, R>(BiFunction<S, T, R> transform) returns IterableMap<S, R>
```

Creates an iterable map with the same keys as map and values generated by running each property of map thru iteratee.
The iteratee is invoked with two arguments: (key, value)

### `mapKeys`

```
IterableMap<S, T>.mapKeys<S, T, R>(BiFunction<S, T, R> transform) returns IterableMap<R, T>
```

Creates an iterable map with the same values as map and keys generated by running each key of map thru iteratee.
The iteratee is invoked with two arguments: (key, value)

### `toPairs`

```
IterableMap<S, T>.toPairs<S, T>() returns LinkedList<Pair<S, T>>
```

Converts an interable map to a list of pairs for each key, value pair.

### `fromPairs`

```
LinkedList<Pair<S, T>>.fromPairs<S, T>() returns IterableMap<S, T>
```

Takes a list of key value pairs and transforms them into a iterable map.

### `chunk`

```
LinkedList<T>.chunk<T>(int size) returns LinkedList<LinkedList<T>>
```

Creates a list of elements split into groups the length of size. If list can't be split evenly, the final chunk will be the remaining elements.

### `pull`

```
LinkedList<T>.pull<T>(T pull) returns LinkedList<T>
```

Removes all given values from list.
