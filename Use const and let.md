### Use const and let

#typescript #good-practice 

**Always** use `const` or `let` to declare variables. Use `const` by default, unless a variable needs to be reassigned. **Never** use `var`.

```ts
const foo = otherValue; // Use if "foo" never changes.
let bar = someValue; // Use if "bar" is ever assigned into later on.
```

`const` and `let` are block scoped, like variables in most other languages. `var` in JavaScript is function scoped, which can cause difficult to understand bugs. Don't use it.

```ts
var foo = someValue; // Don't use - var scoping is complex and causes bugs.
```

Variables _must not_ be used before their declaration.