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

Using Array methods to compute new values while rejecting certain entries can currently be solved in 4 predominant ways:

- Use `.filter` and `.map`, invoking two iterations over the array and allocating two sets of arrays.
- Use `.flatMap` with a ternary inside.
- Use a for loop, creating an empty array and pushing into it conditionally for each iteration of the for loop.
- Use some kind of reducer function, which can have a variety of different styles.

```js
const names = accounts.filter((a) => a.role === "admin").map((a) => a.name)

const names = accounts.flatMap((a) => a.role === "admin" ? [a.name] : [])

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
(arguably) improving readability. `.flatMap()` has been [suggested](https://github.com/tc39/proposal-array-filtering/issues/12#issuecomment-592192544)
as a reason why `.filterMap()` is not needed, but it is debatable which one is more readable.

## Other languages

- [Ruby has an `Enumerable#filter_map` method][ruby], which returns an Array containing each truthy value returned by
  the block.

## Polyfill

- None as of yet.

[ruby]: https://ruby-doc.org/core-2.7.0/Array.html#method-i-compact
