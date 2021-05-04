# Map, reduce, filter

Here's a short and basic discussion on the three Javascript array methods `map`,
`reduce`, and `filter`. The most important thing to remember is that given an
array, we may either

1. Access an element at a specific location.
2. Access every element in the array, one by one, from the start of the array to the end.

It is to the second mode of accessing for which the methods `map`, `reduce`, and `filter` are designed.

### `map`

`map` takes each element of the array and transforms it to something according to a provided callback. So, `map` tranforms an array to a new array of the same length, it's just that the elements could be changed.

A simple example:

```js
> [1, 2, 3].map(i => i * 2)
[ 2, 4, 6 ]
```

### `filter`

`filter` takes each element of an array and either throws it or keeps it according to some condition set by a provided callback. So, `filter` also transforms an array to a new array,
where the length of the latter is at most equal to that of the former, and more importantly,
its elements are just elements in the former (no transformations of the elements).

A simple example:

```js
> [1, 2, 3].filter(i => i > 1)
[ 2, 3 ]
```

### `reduce`

`reduce` takes each element of an array and uses it according to a rule provided by a provided callback. `reduce` may or may not return a new array, it all depends on the way each element is used.

A simple example (note the signature of the callback, in addition to the value of each element there's also
an **accumulator** value):

```js
> [1, 2, 3].reduce((a, v) => a + v)
6
```

A slightly more involved example (this flattens a nested array of one-level depth). Note the empty array `[]` supplied at the end, it is used as the initial value of the accumulator.

```js
> [1, [2, 3], [4, 5, 6]].reduce((a, v) => a.concat(Array.isArray(v) ? v : [v]), [])
[ 1, 2, 3, 4, 5, 6 ]
```

Let's combine what we've learned to solve a small problem: given an array of characters, e.g.

```js
["a", "b", "c", ["d", "a", [[["a", "b"]]], "c", "e"]];
```

we want to count the number of occurrences of a specific character, say `a`. The
answer is of course `3`.

To solve this problem, we'll convert character that's not an `a` into a `1`,
and every other character a `0`. Then we'll flatten the array and do a summation.
The final code is

```js
function count(c, arr) {
  return arr
    .map((e) => {
      if (Array.isArray(e)) {
        return count(c, e);
      }
      return e === c ? 1 : 0;
    })
    .flat(Infinity)
    .reduce((a, v) => a + v);
}
```

A slightly different way of solving this problem, which does not involve
using `flat`, is

```js
function count(c, arr) {
  if (arr.every((e) => typeof e === "number")) {
    return arr.reduce((a, c) => a + c);
  }
  return count(
    c,
    arr.map((e) => {
      if (Array.isArray(e)) {
        return count(c, e);
      }
      return e === c ? 1 : 0;
    })
  );
}
```

Here, if we encounter any element that is an array, we reduce it to a number.
