#typescript #good-practice 

_Do not_ use the `Array()` constructor, with or without `new`. It has confusing and contradictory usage:

```ts
const a = new Array(2); // [undefined, undefined]
const b = new Array(2, 3); // [2, 3];
```

Instead, always use bracket notation to initialize arrays, or `from` to initialize an `Array` with a certain size:

```ts
const a = [2];
const b = [2, 3];

// Equivalent to Array(2):
const c = [];
c.length = 2;

// [0, 0, 0, 0, 0]
Array.from < number > { length: 5 }.fill(0);
```