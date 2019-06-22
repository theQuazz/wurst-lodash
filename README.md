![](https://i.imgur.com/p1HsMJZ.jpg)

# Lodash

[![CircleCI](https://circleci.com/gh/theQuazz/wurst-lodash.svg?style=svg)](https://circleci.com/gh/theQuazz/wurst-lodash)

Functional Programming Utilities for [WurstScript](https://wurstlang.org/) inspired by [Lodash](https://lodash.com/docs/)

- [`package Lodash`](#package-lodash)
    - [`ownList`](#ownList)
    - [`ownMap`](#ownMap)
    - [`owned`](#owned)
    - [`range`](#range)
    - [`rangeStep`](#rangeStep)
    - [`pair`](#pair)
    - [`makeMap`](#makeMap)
    - [`class Ownable`](#class-ownable)
        - [`own`](#own)
        - [`maybeFree`](#maybeFree)
    - [`class Range`](#class-range)
        - [`reset`](#reset)
        - [`toList`](#toList)
    - [`class Pair`](#class-pair)
        - [`head`](#head)
        - [`tail`](#tail)
- [`package LodashExtensions`](#package-lodashextensions)
    - [`any`](#any)
    - [`chunk`](#chunk)
    - [`difference`](#difference)
    - [`drop`](#drop)
    - [`each`](#each)
    - [`equals`](#equals)
    - [`every`](#every)
    - [`filter`](#filter)
    - [`find`](#find)
    - [`findLast`](#findLast)
    - [`flatten`](#flatten)
    - [`foldl`](#foldl)
    - [`foldr`](#foldr)
    - [`fromPairs`](#fromPairs)
    - [`groupBy`](#groupBy)
    - [`indexBy`](#indexBy)
    - [`intersection`](#intersection)
    - [`keys`](#keys)
    - [`length`](#length)
    - [`map`](#map)
    - [`mapKeys`](#mapKeys)
    - [`mapValues`](#mapValues)
    - [`max`](#max)
    - [`min`](#min)
    - [`pull`](#pull)
    - [`sum`](#sum)
    - [`take`](#take)
    - [`takeWhile`](#takeWhile)
    - [`toPairs`](#toPairs)
    - [`union`](#union)
    - [`uniq`](#uniq)
    - [`uniqBy`](#uniqBy)
    - [`values`](#values)
    - [`zipObject`](#zipObject)

## `package Lodash`

This library provides utility functions for operating on `LinkedList`
and `IterableMap` objects as well as utility classes `Range` and `Pair`.
These functions will attempt to destroy the lists and maps
that they act upon unless they are explicitly owned.

#### `ownList`

```typescript
function ownList<T>(LinkedList<T> list) returns OwnedLinkedList<T>
```

Own a list preventing automatic deletion by Lodash methods. Example:

```typescript
let list = asList(1, 2, 3)
let myOwnedList = ownList(list)
```

#### `ownMap`

```typescript
function ownMap<K, V>(IterableMap<K, V> map) returns OwnedIterableMap<K, V>
```

Own a map preventing automatic deletion by Lodash methods. Example:

```typescript
let map = asList(pair(1, 2), pair(2, 3)).fromPairs()
let myOwnedMap = ownMap(map)
```

#### `owned`

```typescript
static function Callable.owned(Callable func) returns Callable
```

Create an owned callable. Example:

```typescript
let myOwnedFunc = Function<int, string>.owned(x -> x.toString())
```

#### `range`

```typescript
function range(int min, int max, int incr) returns Range

function range(int min, int max) returns Range

function range(int max) returns Range

function range() returns Range
```

Creates a range which is iterable from start to finish by incr. Example:

```typescript
let x = new LinkedList<int>
for i from range(0, 10)
    x.add(i)
x // => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

let y = new LinkedList<int>
for i from range(4, 10, 2)
    x.add(i)
x // => [4, 6, 8]
```

#### `rangeStep`

```typescript
function rangeStep(int max, int incr) returns Range
```

Creates a range which is iterable from 0 to finish by incr. Example:

```typescript
let y = new LinkedList<int>
for i from rangeStep(10, 2)
    x.add(i)
x // => [0, 2, 4, 6, 8]
```

#### `pair`

```typescript
function pair<A, B>(A a, B b) returns Pair<A, B>
```

Creates a tuple. Example:

```typescript
let myBinding = pair(1, "foo")
```

#### `makeMap`

```typescript
function makeMap<K, V>(vararg Pair<K, V> elems) returns IterableMap<K, V>
```

Make a map from a list of pairs. Example:

```typescript
makeMap(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c"),
)
// => {
//   1 => "a",
//   2 => "b",
//   3 => "c"
// }
```

### `class Ownable`

```typescript
abstract class Ownable
```

Represents an ownable object exposing two methods: `own`, to claim
ownership of an object, and `maybeFree`, which will destroy the object
if unowned.

#### `own`

```typescript
function Ownable.own()
```

Own's this callable to prevent it from being freed automatically.
You must manually `destroy` this object to free it. Example:

```typescript
let myPair = pair(1, "a")..own()

// this line would free `myPair` unless it was owned
let myMap = makeMap(myPair)

// do more stuff with `myPair

destroy myPair

// Callables can also be owned
Predicate<int> isEven = x -> x mod 2 == 0
isEven.own()
```

#### `maybeFree`

```typescript
function Ownable.maybeFree() returns bool
```

Destroys this callable if it has not been owned. Returns true if the
object was destroyed, otherwise false. Example:

```typescript
let myOwnedPair = pair(1, "a")..own()
let myUnownedPair = pair(2, "b")

myOwnedPair.maybeFree() // => false
myUnownedPair.maybeFree() // => true
```

### `class Range`

```typescript
class Range extends Ownable
    construct(int start, int finish, int incr)
```

Represents a range of numbers. Can be iterated. Default start is `0`,
default finish is `INT_MAX`, default increment amount is `1`. Increment
can be negative for a backwards range. Example:

```typescript
let range = new Range(0, 10, 1)
```

#### `reset`

```typescript
function Range.reset()
```

Reset this iterator. Example:

```typescript
let myRange = range(10)
range.next() // => 0
range.reset()
range.next() // => 0
```

#### `toList`

```typescript
function Range.toList() returns LinkedList<int>
```

Converts a range to a list. Example:

```typescript
range(10).toList() // => [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### `class Pair`

```typescript
class Pair<A, B> extends Ownable
    A a
    B b

    construct(A a, B b)
```

A tuple which can be cast to index for use in data types. Example:

```typescript
let pair = new Pair(1, "a")
```

#### `head`

```typescript
function Pair<A, B>.head() returns A
```

Get the first element of the pair. Example:

```typescript
let myPair = pair(1, "a")
myPair.head() // => 1
```

#### `tail`

```typescript
function Pair<A, B>.tail() returns B
```

Get the last element of the pair. Example:

```typescript
let myPair = pair(1, "a")
myPair.tail() // => "a"
```

## `package LodashExtensions`

This package provides various extension methods for `LinkedList` and `IterableMap`. Similar to the WurstFP API, these methods will attempt to
destroy the closures and objects they are invoked upon and passed.

#### `equals`

```typescript
function LinkedList<T>.equals<T>(LinkedList<T> b) returns bool

function IterableMap<K, V>.equals<K, V>(IterableMap<K, V> b) returns bool
```

Checks for equality of two lists/maps. Example:

```typescript
let a = asList(1, 2, 3)
let b = asOwnedList(1, 2, 3)

a.equals(b) // => true
```

#### `takeWhile`

```typescript
function LinkedList<T>.takeWhile<T>(BiFunction<T, int, bool> predicate) returns LinkedList<T>
```

Creates a slice of list with elements taken from the beginning. Elements are taken until predicate returns falsey.
The predicate is invoked with one to three argument(s): (value, index, list). Example:

```typescript
range(5).takeWhile((x, _i) -> x < 3) // => [0, 1, 2]
```

#### `take`

```typescript
function LinkedList<T>.take<T>(int numElems) returns LinkedList<T>
```

Creates a slice of list with numElems elements taken from the beginning.
Example:

```typescript
asList(1, 2, 3, 4, 5).take(4) // => [1, 2, 3, 4]
```

#### `foldl`

```typescript
function LinkedList<T>.foldl<Q, T>(Q startValue, BiFunction<T, Q, Q> transform) returns Q
```

Reduces collection to a value which is the accumulated result of running each element in collection thru iteratee,
where each successive invocation is supplied the return value of the previous. The order if iteration is from left
to right. The transform is invoked with two arguments: (accumulator, value). Example:

```typescript
asList("h", "e", "l", "l", "o").foldl("", (acc, val) -> acc + val)
// => "hello"
```

#### `foldr`

```typescript
function LinkedList<T>.foldr<Q, T>(Q startValue, BiFunction<T, Q, Q> transform) returns Q
```

Reduces collection to a value which is the accumulated result of running
each element in collection thru iteratee, where each successive
invocation is supplied the return value of the previous. The order if
iteration is from right to left. The transform is invoked with two
arguments: (accumulator, value). Example:

```typescript
asList("h", "e", "l", "l", "o").foldr("", (acc, val) -> acc + val)
// => "olleh"
```

#### `every`

```typescript
function LinkedList<T>.every<T>(Predicate<T> predicate) returns bool
```

Checks if predicate returns truthy for all elements of collection.
Iteration is stopped once predicate returns falsey.
The predicate is invoked with one argument: (value). Example:

```typescript
asList(1, 2, 3, 4, 5).every(x -> x < 9) // => true
asList(1, 2, 3, 4, 5).every(x -> x < 5) // => false
```

#### `any`

```typescript
function LinkedList<T>.any<T>(Predicate<T> predicate) returns bool
```

Checks if predicate returns truthy for any element of collection. Iteration is stopped once predicate returns truthy.
The predicate is invoked with one argument: (value). Example:

```typescript
asList(1, 2, 3, 4, 5).any(x -> x < 2) // => true
asList(1, 2, 3, 4, 5).any(x -> x > 9) // => false
```

#### `keys`

```typescript
function IterableMap<T, Q>.keys<T, Q>() returns LinkedList<T>
```

Creates a list of the keys of map. Example:

```typescript
let map = makeMap(
    pair("a", "apple"),
    pair("p", "pear"),
    pair("o", "orange")
).keys()
// => ["a", "p", "o"]
```

#### `values`

```typescript
function IterableMap<T, Q>.values<T, Q>() returns LinkedList<Q>
```

Creates a list of the values of map. Example:

```typescript
let map = makeMap(
    pair("a", "apple"),
    pair("p", "pear"),
    pair("o", "orange")
).keys()
// => ["apple", "pear", "orange"]
```

#### `map`

```typescript
function LinkedList<T>.map<T, Q>(BiFunction<T, int, Q> transform) returns LinkedList<Q>

function IterableMap<S, T>.map<S, T, R>(BiFunction<S, T, R> transform) returns LinkedList<R>
```

Creates a list of values by running each element in collection thru transform. The
transform for lists is invoked with one or two argument(s): (value, index).
The transform for maps is invoked with two arguments: (key, value).
Example:

```typescript
asList(1, 2, 3).map(x -> x.squared()) // => [1, 4, 9]
```

#### `flatten`

```typescript
function LinkedList<LinkedList<T>>.flatten<T>() returns LinkedList<T>
```

Flattens list a level less deep. Example:

```typescript
asList(
    asList(1, 2, 3),
    asList(4, 5),
    asList(6)
).flatten()
// => [1, 2, 3, 4, 5, 6]
```

#### `drop`

```typescript
function LinkedList<T>.drop<T>(int numElems) returns LinkedList<T>
```

Creates a slice of list with numELems elements dropped from the
beginning. Example:

```typescript
asList(1, 2, 3, 4, 5, 6).drop(2) // => [3, 4, 5, 6]
```

#### `filter`

```typescript
function LinkedList<T>.lodashFilter<T>(Predicate<T> filter) returns LinkedList<T>
```

Iterates over elements of list, returning a list of all elements
predicate returns truthy for.
The predicate is invoked with one argument: (value).

```typescript
asList(1, 2, 3, 4, 5).lodashFilter(x -> x mod 2 == 0)
// => [2, 4]
```

#### `sum`

```typescript
function LinkedList<int>.sum() returns int

function LinkedList<real>.sum() returns real
```

Computes the sum of the values in list. Example:

```typescript
asList(1, 2, 3).sum() // => 6

asList(1.1, 2.2, 3.3).sum() // => 6.6
```

#### `max`

```typescript
function LinkedList<int>.max() returns int

function LinkedList<real>.max() returns real
```

Computes the max of the values in list. Example:

```typescript
asList(1, 2, 3).max() // => 3

asList(1.1, 2.2, 3.3).max() // => 3.3
```

#### `min`

```typescript
function LinkedList<int>.min() returns int

function LinkedList<real>.min() returns real
```

Computes the min of the values in list. Example:

```typescript
asList(1, 2, 3).min() // => 1

asList(1.1, 2.2, 3.3).min() // => 1.1
```

#### `length`

```typescript
function LinkedList<T>.length<T>() returns int
```

Gets the length of list and maybe frees it. Example:

```typescript
asList(1, 2, 3).length() // => 3
```

#### `each`

```typescript
function LinkedList<T>.each<T>(VoidFunction<T> func)

function LinkedList<T>.each<T>(VoidBiFunction<T, int> func)
```

Iterates over elements of collection and invokes iteratee for each element.
The iteratee is invoked with one or two argument(s): (value, index).
Example:

```typescript
asList(1, 2, 3).each(x -> print(x))
```

#### `zipObject`

```typescript
function LinkedList<A>.zipObject<A, B>(LinkedList<B> lst) returns IterableMap<A, B>
```

Creates an IterableMap with the keys from A and the values from B.
Example:

```typescript
asList(1, 2, 3).zipObject(asList("a", "b", "c"))
// => { 1 => "a", 2 => "b", 3 => "c" }
```

#### `zip`

```typescript
function zip<A, B>(LinkedList<A> a, LinkedList<B> b) returns LinkedList<Pair<A, B>>
```

Creates a list of grouped elements, the first of which contains the
first elements of the given lists, the second of which contains the
second elements of the given lists.

```typescript
let keys = asList(1, 2, 3)
let values = asList("a", "b", "c")

zip(keys, values)
// => { 1 => "a", 2 => "b", 3 => "c" }
```

#### `uniq`

```typescript
function LinkedList<T>.uniq<T>() returns LinkedList<T>
```

Creates a duplicate-free version of a list in which only the first occurrence
of each element is kept. The order of result values is determined by the order
they occur in the list. Example:

```typescript
asList(1, 3, 2, 1, 4, 2, 5).uniq() // => [1, 3, 2, 4, 5]
```

#### `uniqBy`

```typescript
function LinkedList<T>.uniqBy<T, R>(Function<T, R> func) returns LinkedList<T>
```

Creates a duplicate-free version of a list in which only the first occurrence
of each element is kept as compared by func. The order of result values is
determined by the order they occur in the list. The iteratee is invoked with
one argument: (value). Example:

```typescript
asList(1.0, 1.1, 1.2, 2.0, 2.1, 2.2).uniqBy(x -> x.floor())
// => [1.0, 2.0]
```

#### `union`

```typescript
function LinkedList<T>.union<T>(LinkedList<T> lst) returns LinkedList<T>
```

Creates a list of unique values, in order, from the given lists. Example:

```typescript
asList(1, 2, 3).union(asList(3, 4, 5)) // => [1, 2, 3, 4, 5]
```

#### `intersection`

```typescript
function LinkedList<T>.intersection<T>(LinkedList<T> lst) returns LinkedList<T>
```

Creates an array of unique values that are included in both given lists. Example:

```typescript
asList(1, 2, 3).intersection(asList(3, 4, 5)) // => [3]
```

#### `difference`

```typescript
function LinkedList<T>.difference<T>(LinkedList<T> lst) returns LinkedList<T>
```

Creates an array of array values not included in the other given lists. Example:

```typescript
asList(1, 2, 3).difference(asList(3, 4, 5)) // => [1, 2]
```

#### `indexBy`

```typescript
function LinkedList<T>.indexBy<T, R>(Function<T, R> idx) returns IterableMap<R, T>
```

Creates an iterable map composed of keys generated from the results of running each element of collection thru iteratee.
The corresponding value of each key is the last element responsible for generating the key. The iteratee is invoked with
one argument: (value). Example:

```typescript
let list = asList(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c")
)

list.indexBy(x -> x.b)
// => {
//   "a" => pair(1, "a"),
//   "b" => pair(2, "b"),
//   "c" => pair(3, "c")
// }
```

#### `groupBy`

```typescript
function LinkedList<T>.groupBy<T, R>(Function<T, R> idx) returns IterableMap<R, LinkedList<T>>
```

Creates an iterable map composed of keys generated from the results of running each element of collection thru iteratee.
The order of grouped values is determined by the order they occur in collection. The corresponding value of each key is
a list of elements responsible for generating the key. The iteratee is invoked with one argument: (value). Example:

```typescript
let list = asList(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c"),
    pair(4, "a"),
    pair(5, "c")
)

list.groupBy(x -> x.b)
// => {
//   "a" => [pair(1, "a"), pair(4, "a")],
//   "b" => [pair(2, "b")],
//   "c" => [pair(3, "c"), pair(5, "c")]
// }
```

#### `mapValues`

```typescript
function IterableMap<S, T>.mapValues<S, T, R>(BiFunction<S, T, R> transform) returns IterableMap<S, R>
```

Creates an iterable map with the same keys as map and values generated by running each property of map thru iteratee.
The iteratee is invoked with two arguments: (key, value). Example:

```typescript
let map = makeMap(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c")
)

map.mapValues(x -> x.toUpperCase())
// => {
//   1 => "A",
//   2 => "B",
//   3 => "C"
// }
```

#### `mapKeys`

```typescript
function IterableMap<S, T>.mapKeys<S, T, R>(BiFunction<S, T, R> transform) returns IterableMap<R, T>
```

Creates an iterable map with the same values as map and keys generated by running each key of map thru iteratee.
The iteratee is invoked with two arguments: (key, value). Example:

```typescript
let map = makeMap(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c")
)

map.mapValues(x -> x + 1)
// => {
//   2 => "a",
//   3 => "b",
//   4 => "c"
// }
```

#### `toPairs`

```typescript
function IterableMap<S, T>.toPairs<S, T>() returns LinkedList<Pair<S, T>>
```

Converts an interable map to a list of pairs for each key, value pair.
Example:

```typescript
let map = makeMap(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c")
)

map.toPairs()
// => [
//   pair(1, "a"),
//   pair(2, "b"),
//   pair(3, "c")
// ]
```

#### `fromPairs`

```typescript
function LinkedList<Pair<S, T>>.fromPairs<S, T>() returns IterableMap<S, T>
```

Takes a list of key value pairs and transforms them into a iterable map.
Example:

```typescript
let map = asList(
    pair(1, "a"),
    pair(2, "b"),
    pair(3, "c")
)

map.fromPairs()
// => {
//   1 => "a",
//   2 => "b",
//   3 => "c"
// }
```

#### `chunk`

```typescript
function LinkedList<T>.chunk<T>(int size) returns LinkedList<LinkedList<T>>
```

Creates a list of elements split into groups the length of size. If list can't be split evenly, the final chunk will be the remaining elements. Example:

```typescript
asList(1, 2, 3, 4, 5, 6, 7, 8).chunk(3)
// => [[1, 2, 3], [4, 5, 6], [7, 8]]
```

#### `pull`

```typescript
function LinkedList<T>.pull<T>(T pull) returns LinkedList<T>
```

Removes all given values from list. Example:

```typescript
asList(1, 2, 3, 4, 5, 4, 3, 2, 1).pull(3)
// => [1, 2, 4, 5, 4, 2, 1]
```

#### `find`

```typescript
function LinkedList<T>.find<T>(Predicate<T> func) returns T
```

Returns the first element matching the predicate function. Example:

```typescript
asList(pair(1, "a"), pair(2, "b"), pair(3, "c"), pair(2, "d")).find(x -> x.a == 2)
// => pair(2, "b")
```

#### `findLast`

```typescript
function LinkedList<T>.findLast<T>(Predicate<T> func) returns T
```

Returns the last element matching the predicate function. Example:

```typescript
asList(pair(1, "a"), pair(2, "b"), pair(3, "c"), pair(2, "d")).findLast(x -> x.a == 2)
// => pair(2, "d")
```
