# proposal-array-filterMap

A proposal to create a `filterMap` Array method that returns a new array containing each truthy value returned by the
mapping function. Think of it like `.filter+.map`.

```js
const accounts = [
  { role: "admin", name: "Alice" },
  { role: "admin", name: "Bob" },
  { role: "user", name: "Carol" },
  { role: "user", name: "Carlos" },
  { role: "user", name: "Charlie" },
]

// Using .filter(...).map(...)
const names = accounts.filter((a) => a.role === "admin").map((a) => a.name)
// =>  ['Alice', 'Bob']

// Using .filterMap()
const names = accounts.filterMap((a) => a.role === "admin" && a.name)
// =>  ['Alice', 'Bob']
```

## Champions

- Keith Cirkel ([@keithamus](https://github.com/keithamus/))

## Status

Current [Stage](https://tc39.es/process-document/): 0

## Motivation

Using Array methods to compute new values while rejecting certain entries can currently be solved in 3 predominant ways:

- Use `.filter` and `.map`, invoking two iterations over the array and allocating two sets of arrays.
- Use a for loop, creating an empty array and pushing into it conditionally for each iteration of the for loop.
- Use some kind of reducer function, which can have a variety of different styles.

```js
const names = accounts.filter((a) => a.role === "admin").map((a) => a.name)

const names = []
for (const a of accounts) {
  if (a.role === "admin") names.push(a.name)
}

const names = accounts.reduce((names, a) => {
  if (a.role === "admin") names.push(a.name)
  // Or
  if (a.role === "admin") return names.concat(a.name)
  // Or
  if (a.role !== "admin") return [...names, a.name]

  return names
}, [])
```

These all feel like they come with some kind of compromise. The filter+map is probably the most readable, but comes at
the cost of lowest performance among all three. `filterMap()` would solve the performance implications while also
(arguably) improve readability.

## Other languages

- [Ruby has an `Enumerable#filter_map` method][ruby], which returns an Array containing each truthy value returned by
  the block.
- [Rust has `std::iter::FilterMap`][rust], which returns an iterator that uses `f` to both filter and map elements from iter.

## Polyfill

- None as of yet.

[ruby]: https://ruby-doc.org/3.2.1/Enumerable.html#method-i-filter_map
[rust]: https://doc.rust-lang.org/std/iter/struct.FilterMap.html
