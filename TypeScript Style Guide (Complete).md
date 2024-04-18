#source #typescript #styleguide  #documentation 

# TOC

- [TOC](#toc)
- [Source file structure](#source-file-structure)
  - [`@fileoverview` JSDoc](#fileoverview-jsdoc)
  - [Imports](#imports)
    - [Import paths](#import-paths)
    - [Namespace versus named imports](#namespace-versus-named-imports)
  - [Exports](#exports)
- [Language features](#language-features)
  - [Local variable declarations](#local-variable-declarations)
    - [Use const and let](#use-const-and-let)
    - [One variable per declaration](#one-variable-per-declaration)
  - [Array literals](#array-literals)
    - [Do not use the `Array` constructor](#do-not-use-the-array-constructor)
    - [Do not define properties on arrays](#do-not-define-properties-on-arrays)
    - [Using spread syntax](#using-spread-syntax)
    - [Array destructuring](#array-destructuring)
  - [Object literals](#object-literals)
    - [Do not use the `Object` constructor](#do-not-use-the-object-constructor)
    - [Iterating objects](#iterating-objects)
    - [Using spread syntax](#using-spread-syntax-1)
    - [Computed property names](#computed-property-names)
    - [Object destructuring](#object-destructuring)
  - [Functions](#functions)
    - [Terminology](#terminology)
    - [Prefer function declarations for named functions](#prefer-function-declarations-for-named-functions)
    - [Nested functions](#nested-functions)
    - [Do not use function expressions](#do-not-use-function-expressions)
    - [Arrow function bodies](#arrow-function-bodies)
    - [Rebinding `this`](#rebinding-this)
    - [Prefer passing arrow functions as callbacks](#prefer-passing-arrow-functions-as-callbacks)
    - [Arrow functions as properties](#arrow-functions-as-properties)
    - [Parameter initializers](#parameter-initializers)
    - [Prefer rest and spread when appropriate](#prefer-rest-and-spread-when-appropriate)
    - [Formatting functions](#formatting-functions)
  - [Primitive literals](#primitive-literals)
    - [String literals](#string-literals)
      - [Use single quotes](#use-single-quotes)
      - [No line continuations](#no-line-continuations)
      - [Template literals](#template-literals)
    - [Number literals](#number-literals)
    - [Type coercion](#type-coercion)
      - [Implicit coercion](#implicit-coercion)
  - [Control structures](#control-structures)
    - [Control flow statements and blocks](#control-flow-statements-and-blocks)
      - [Assignment in control statements](#assignment-in-control-statements)
      - [Iterating containers](#iterating-containers)
    - [Grouping parentheses](#grouping-parentheses)
    - [Exception handling](#exception-handling)
      - [Instantiate errors using `new`](#instantiate-errors-using-new)
      - [Only throw errors](#only-throw-errors)
      - [Catching and rethrowing](#catching-and-rethrowing)
      - [Empty catch blocks](#empty-catch-blocks)
    - [Switch statements](#switch-statements)
    - [Equality checks](#equality-checks)
    - [Type and non-nullability assertions](#type-and-non-nullability-assertions)
      - [Type assertion syntax](#type-assertion-syntax)
      - [Double assertions](#double-assertions)
      - [Type assertions and object literals](#type-assertions-and-object-literals)
    - [Keep try blocks focused](#keep-try-blocks-focused)
- [Naming](#naming)
  - [Identifiers](#identifiers)
    - [Naming style](#naming-style)
    - [Descriptive names](#descriptive-names)
    - [Camel case](#camel-case)
    - [Dollar sign](#dollar-sign)
  - [Rules by identifier type](#rules-by-identifier-type)
    - [Type parameters](#type-parameters)
    - [Test names](#test-names)
    - [`_` prefix/suffix](#_-prefixsuffix)
    - [Imports](#imports-1)
    - [Constants](#constants)
    - [Aliases](#aliases)
- [Type system](#type-system)
  - [Type inference](#type-inference)
    - [Return types](#return-types)
  - [Undefined and null](#undefined-and-null)
    - [Nullable/undefined type aliases](#nullableundefined-type-aliases)
    - [Prefer optional over `|undefined`](#prefer-optional-over-undefined)
  - [Use structural types](#use-structural-types)
  - [Prefer interfaces over type literal aliases](#prefer-interfaces-over-type-literal-aliases)
  - [`Array<T>` Type](#arrayt-type)
  - [Indexable types / index signatures (`{[key: string]: T}`)](#indexable-types--index-signatures-key-string-t)
  - [Mapped and conditional types](#mapped-and-conditional-types)
  - [`any` Type](#any-type)
    - [Providing a more specific type](#providing-a-more-specific-type)
    - [Using `unknown` over `any`](#using-unknown-over-any)
    - [Suppressing `any` lint warnings](#suppressing-any-lint-warnings)
  - [`{}` Type](#-type)
  - [Tuple types](#tuple-types)
  - [Wrapper types](#wrapper-types)
  - [Return type only generics](#return-type-only-generics)
- [Comments and documentation](#comments-and-documentation)
    - [JSDoc versus comments](#jsdoc-versus-comments)
    - [Multi-line comments](#multi-line-comments)
  - [JSDoc general form](#jsdoc-general-form)
  - [Markdown](#markdown)
  - [JSDoc tags](#jsdoc-tags)
  - [Line wrapping](#line-wrapping)
  - [Document all top-level exports of modules](#document-all-top-level-exports-of-modules)
  - [Class comments](#class-comments)
  - [Method and function comments](#method-and-function-comments)
  - [Parameter property comments](#parameter-property-comments)
  - [JSDoc type annotations](#jsdoc-type-annotations)
  - [Make comments that actually add information](#make-comments-that-actually-add-information)
    - [Comments when calling a function](#comments-when-calling-a-function)

# Source file structure

Files consist of the following, **in order**:

1.  [JSDoc with `@fileoverview`, if present](#fileoverview-jsdoc)
2.  [Imports](#imports)
3.  The file’s implementation

## `@fileoverview` JSDoc

A file may have a top-level `@fileoverview` JSDoc. If present, it may provide a description of the file's content, its uses, or information about its dependencies. Wrapped lines are not indented.

Example:

```js
/**
 * @fileoverview Description of file. Lorem ipsum dolor sit amet, consectetur
 * adipiscing elit, sed do eiusmod tempor incididunt.
 */
```

## Imports

| Import type                 | Example                          | Use for                                                                           |
| --------------------------- | -------------------------------- | --------------------------------------------------------------------------------- |
| module[module_import]       | `import \* as foo from '...';`   | TypeScript imports                                                                |
| named[destructuring_import] | `import {SomeThing} from '...';` | TypeScript imports                                                                |
| default                     | `import SomeThing from '...';`   | Only for other external code that requires them                                   |
| side-effect                 | `import '...';`                  | Only to import libraries for their side-effects on load (such as custom elements) |

### Import paths

TypeScript code _must_ use paths to import other TypeScript code. Paths _may_ be relative, i.e. starting with `.` or `..`, or rooted at the base directory, e.g. `root/path/to/file`.

Code _should_ use relative imports (`./foo`) rather than absolute imports `path/to/foo` when referring to files within the same (logical) project as this allows to move the project around without introducing changes in these imports.

Consider limiting the number of parent steps (`../../../`) as those can make module and path structures hard to understand.

```js
import { Symbol1 } from 'path/from/root';
import { Symbol2 } from '../parent/file';
import { Symbol3 } from './sibling';
```

### Namespace versus named imports

Both namespace and named imports can be used.

**Prefer named imports** for symbols used frequently in a file or for symbols that have clear names, for example Jasmine's `describe` and `it`. Named imports can be aliased to clearer names as needed with `as`.

Prefer namespace imports when using many different symbols from large APIs. A namespace import, despite using the `*` character, is not comparable to a wildcard import as seen in other languages. Instead, namespace imports give a name to all the exports of a module, and each exported symbol from the module becomes a property on the module name. Namespace imports can aid readability for exported symbols that have common names like `Model` or `Controller` without the need to declare aliases.

```js
// Bad: overlong import statement of needlessly namespaced names.
import {
  Item as TableviewItem,
  Header as TableviewHeader,
  Row as TableviewRow,
  Model as TableviewModel,
  Renderer as TableviewRenderer,
} from './tableview';

let item: TableviewItem | undefined;
```

```js
// Better: use the module for namespacing.
import * as tableview from './tableview';

let item: tableview.Item | undefined;
```

```js
import * as testing from './testing';

// Bad: The module name does not improve readability.
testing.describe('foo', () => {
  testing.it('bar', () => {
    testing.expect(null).toBeNull();
    testing.expect(undefined).toBeUndefined();
  });
});
```

```js
// Better: give local names for these common functions.
import { describe, it, expect } from './testing';

describe('foo', () => {
  it('bar', () => {
    expect(null).toBeNull();
    expect(undefined).toBeUndefined();
  });
});
```

## Exports

Use named exports in all code:

```js
    // Use named exports:
    export class Foo { ... }
```

Do not use default exports. This ensures that all imports follow a uniform pattern.

```js
    // Do not use default exports:
    export default class Foo { ... } // BAD!
```

Why?

Default exports provide no canonical name, which makes central maintenance difficult with relatively little benefit to code owners, including potentially decreased readability:

```js
import Foo from './bar'; // Legal.
import Bar from './bar'; // Also legal.
```

Named exports have the benefit of erroring when import statements try to import something that hasn't been declared. In `foo.ts`:

```js
const foo = 'blah';
export default foo;
```

And in `bar.ts`:

```js
import { fizz } from './foo';
```

Results in `error TS2614: Module '"./foo"' has no exported member 'fizz'.` While `bar.ts`:

```js
import fizz from './foo';
```

Results in `fizz === foo`, which is probably unexpected and difficult to debug.

Additionally, default exports encourage people to put everything into one big object to namespace it all together:

```js
    export default class Foo {
      static SOME_CONSTANT = ...
      static someHelpfulFunction() { ... }
      ...
    }
```

With the above pattern, we have file scope, which can be used as a namespace. We also have a perhaps needless second scope (the class `Foo`) that can be ambiguously used as both a type and a value in other files.

Instead, prefer use of file scope for namespacing, as well as named exports:

```js
    export const SOME_CONSTANT = ...
    export function someHelpfulFunction()
    export class Foo {
      // only class stuff here
    }
```

# Language features

This section delineates which features may or may not be used, and any additional constraints on their use.

Language features which are not discussed in this style guide may be used with no recommendations of their usage.

## Local variable declarations

### Use const and let

**Always** use `const` or `let` to declare variables. Use `const` by default, unless a variable needs to be reassigned. **Never** use `var`.

```js
const foo = otherValue; // Use if "foo" never changes.
let bar = someValue; // Use if "bar" is ever assigned into later on.
```

`const` and `let` are block scoped, like variables in most other languages. `var` in JavaScript is function scoped, which can cause difficult to understand bugs. Don't use it.

```js
var foo = someValue; // Don't use - var scoping is complex and causes bugs.
```

Variables _must not_ be used before their declaration.

### One variable per declaration

Every local variable declaration declares only one variable: declarations such as `let a = 1, b = 2;` are not used.

## Array literals

### Do not use the `Array` constructor

_Do not_ use the `Array()` constructor, with or without `new`. It has confusing and contradictory usage:

```js
const a = new Array(2); // [undefined, undefined]
const b = new Array(2, 3); // [2, 3];
```

Instead, always use bracket notation to initialize arrays, or `from` to initialize an `Array` with a certain size:

```js
const a = [2];
const b = [2, 3];

// Equivalent to Array(2):
const c = [];
c.length = 2;

// [0, 0, 0, 0, 0]
Array.from < number > { length: 5 }.fill(0);
```

### Do not define properties on arrays

Do not define or use non-numeric properties on an array (other than `length`). Use a `Map` (or `Object`) instead.

### Using spread syntax

Using spread syntax `[...foo];` is a convenient shorthand for shallow-copying or concatenating iterables.

```js
const foo = [1];

const foo2 = [...foo, 6, 7];

const foo3 = [5, ...foo];

foo2[1] === 6;
foo3[1] === 1;
```

When using spread syntax, the value being spread _must_ match what is being created. When creating an array, only spread iterables. Primitives (including `null` and `undefined`) _must not_ be spread.

```js
const foo = [7];
const bar = [5, ...(shouldUseFoo && foo)]; // might be undefined

// Creates {0: 'a', 1: 'b', 2: 'c'} but has no length
const fooStrings = ['a', 'b', 'c'];
const ids = { ...fooStrings };

const foo = shouldUseFoo ? [7] : [];
const bar = [5, ...foo];
const fooStrings = ['a', 'b', 'c'];
const ids = [...fooStrings, 'd', 'e'];
```

### Array destructuring

Array literals may be used on the left-hand side of an assignment to perform destructuring (such as when unpacking multiple values from a single array or iterable). A final rest element may be included (with no space between the `...` and the variable name). Elements should be omitted if they are unused.

```js
const [a, b, c, ...rest] = generateResults();
let [, b, , d] = someArray;
```

Destructuring may also be used for function parameters. Always specify `[]` as the default value if a destructured array parameter is optional, and provide default values on the left hand side:

```js
function destructured([a = 4, b = 2] = []) { … }
```

Disallowed:

```js
function badDestructuring([a, b] = [4, 2]) { … }
```

Tip: For (un)packing multiple values into a function’s parameter or return, prefer object destructuring to array destructuring when possible, as it allows naming the individual elements and specifying a different type for each.

## Object literals

### Do not use the `Object` constructor

The `Object` constructor is disallowed. Use an object literal (`{}` or `{a: 0, b: 1, c: 2}`) instead.

### Iterating objects

Iterating objects with `for (... in ...)` is error prone. It will include enumerable properties from the prototype chain.

Do not use unfiltered `for (... in ...)` statements:

```js
for (const x in someObj) {
  // x could come from some parent prototype!
}
```

Either filter values explicitly with an `if` statement, or use `for (... of Object.keys(...))`.

```js
for (const x in someObj) {
  if (!someObj.hasOwnProperty(x)) continue;
  // now x was definitely defined on someObj
}
for (const x of Object.keys(someObj)) {
  // note: for _of_!
  // now x was definitely defined on someObj
}
for (const [key, value] of Object.entries(someObj)) {
  // note: for _of_!
  // now key was definitely defined on someObj
}
```

### Using spread syntax

Using spread syntax `{...bar}` is a convenient shorthand for creating a shallow copy of an object. When using spread syntax in object initialization, later values replace earlier values at the same key.

```js
const foo = {
  num: 1,
};

const foo2 = {
  ...foo,
  num: 5,
};

const foo3 = {
  num: 5,
  ...foo,
};

foo2.num === 5;
foo3.num === 1;
```

When using spread syntax, the value being spread _must_ match what is being created. That is, when creating an object, only objects may be spread; arrays and primitives (including `null` and `undefined`) _must not_ be spread. Avoid spreading objects that have prototypes other than the Object prototype (e.g. class definitions, class instances, functions) as the behavior is unintuitive (only enumerable non-prototype properties are shallow-copied).

```js
const foo = { num: 7 };
const bar = { num: 5, ...(shouldUseFoo && foo) }; // might be undefined

// Creates {0: 'a', 1: 'b', 2: 'c'} but has no length
const fooStrings = ['a', 'b', 'c'];
const ids = { ...fooStrings };

const foo = shouldUseFoo ? { num: 7 } : {};
const bar = { num: 5, ...foo };
```

### Computed property names

Computed property names (e.g. `{['key' + foo()]: 42}`) are allowed, and are considered dict-style (quoted) keys (i.e., must not be mixed with non-quoted keys) unless the computed property is a [symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) (e.g. `[Symbol.iterator]`).

### Object destructuring

Object destructuring patterns may be used on the left-hand side of an assignment to perform destructuring and unpack multiple values from a single object.

Destructured objects may also be used as function parameters, but should be kept as simple as possible: a single level of unquoted shorthand properties. Deeper levels of nesting and computed properties may not be used in parameter destructuring. Specify any default values in the left-hand-side of the destructured parameter (`{str = 'some default'} = {}`, rather than `{str} = {str: 'some default'}`), and if a destructured object is itself optional, it must default to `{}`.

Example:

```js
interface Options {
  /** The number of times to do something. */
  num?: number;

  /** A string to do stuff to. */
  str?: string;
}

function destructured({ num, str = 'default' }: Options = {}) {}
```

Disallowed:

```js
function nestedTooDeeply({ x: { num, str } }: { x: Options }) {}
function nontrivialDefault(
  { num, str }: Options = { num: 42, str: 'default' }
) {}
```

## Functions

### Terminology

There are many different types of functions, with subtle distinctions between them. This guide uses the following terminology, which aligns with [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions):

- `function declaration`: a declaration (i.e. not an expression) using the `function` keyword
- `function expression`: an expression, typically used in an assignment or passed as a parameter, using the `function` keyword
- `arrow function`: an expression using the `=>` syntax
- `block body`: right hand side of an arrow function with braces
- `concise body`: right hand side of an arrow function without braces

_Note_: Methods and classes/constructors are not covered in this section.

### Prefer function declarations for named functions

Prefer function declarations over arrow functions or function expressions when defining named functions.

```js
// Prefer
function foo() {
  return 42;
}

// Avoid
const foo = () => 42;
```

Arrow functions _may_ be used, for example, when an explicit type annotation is required.

```ts
interface SearchFunction {
  (source: string, subString: string): boolean;
}

const fooSearch: SearchFunction = (source, subString) => { ... };
```

### Nested functions

Functions nested within other methods or functions _may_ use function declarations or arrow functions, as appropriate. In method bodies in particular, arrow functions are preferred because they have access to the outer `this`.

### Do not use function expressions

Do not use function expressions. Use arrow functions instead.

```js
// Must
bar(() => { this.doSomething(); })

// Must not
bar(function() { ... })
```

**Exception:** Function expressions _may_ be used _only if_ code has to dynamically rebind `this` (but this is discouraged), or for generator functions (which do not have an arrow syntax).

### Arrow function bodies

Use arrow functions with concise bodies (i.e. expressions) or block bodies as appropriate.

```js
// Top level functions use function declarations.
function someFunction() {
  // Block bodies are fine:
  const receipts = books.map((b: Book) => {
    const receipt = payMoney(b.price);
    recordTransaction(receipt);
    return receipt;
  });

  // Concise bodies are fine, too, if the return value is used:
  const longThings = myValues.filter(v => v.length > 1000).map(v => String(v));

  function payMoney(amount: number) {
    // function declarations are fine, but must not access `this`.
  }

  // Nested arrow functions may be assigned to a const.
  const computeTax = (amount: number) => amount * 0.12;
}
```

Only use a concise body if the return value of the function is actually used. The block body makes sure the return type is `void` then and prevents potential side effects.

```js
// BAD: use a block body if the return value of the function is not used.
myPromise.then(v => console.log(v));
// BAD: this typechecks, but the return value still leaks.
let f: () => void;
f = () => 1;

// GOOD: return value is unused, use a block body.
myPromise.then(v => {
  console.log(v);
});
// GOOD: code may use blocks for readability.
const transformed = [1, 2, 3].map(v => {
  const intermediate = someComplicatedExpr(v);
  const more = acrossManyLines(intermediate);
  return worthWrapping(more);
});
// GOOD: explicit `void` ensures no leaked return value
myPromise.then(v => void console.log(v));
```

Tip: The `void` operator can be used to ensure an arrow function with an expression body returns `undefined` when the result is unused.

### Rebinding `this`

Function expressions and function declarations _must not_ use `this` unless they specifically exist to rebind the `this` pointer. Rebinding `this` can in most cases be avoided by using arrow functions or explicit parameters.

```js
function clickHandler() {
  // Bad: what's `this` in this context?
  this.textContent = 'Hello';
}
// Bad: the `this` pointer reference is implicitly set to document.body.
document.body.onclick = clickHandler;
```

```js
// Good: explicitly reference the object from an arrow function.
document.body.onclick = () => {
  document.body.textContent = 'hello';
};
// Alternatively: take an explicit parameter
const setTextFn = (e: HTMLElement) => {
  e.textContent = 'hello';
};
document.body.onclick = setTextFn.bind(null, document.body);
```

Prefer arrow functions over other approaches to binding `this`, such as `f.bind(this)`, `goog.bind(f, this)`, or `const self = this`.

### Prefer passing arrow functions as callbacks

Callbacks can be invoked with unexpected arguments that can pass a type check but still result in logical errors.

Avoid passing a named callback to a higher-order function, unless you are sure of the stability of both functions' call signatures. Beware, in particular, of less-commonly-used optional parameters.

```js
// BAD: Arguments are not explicitly passed, leading to unintended behavior
// when the optional `radix` argument gets the array indices 0, 1, and 2.
const numbers = ['11', '5', '10'].map(parseInt);
// > [11, NaN, 2];
```

Instead, prefer passing an arrow-function that explicitly forwards parameters to the named callback.

```js
// GOOD: Arguments are explicitly passed to the callback
const numbers = ['11', '5', '3'].map(n => parseInt(n));
// > [11, 5, 3]

// GOOD: Function is locally defined and is designed to be used as a callback
function dayFilter(element: string | null | undefined) {
  return element != null && element.endsWith('day');
}

const days = ['tuesday', undefined, 'juice', 'wednesday'].filter(dayFilter);
```

### Arrow functions as properties

Classes usually _should not_ contain properties initialized to arrow functions. Arrow function properties require the calling function to understand that the callee's `this` is already bound, which increases confusion about what `this` is, and call sites and references using such handlers look broken (i.e. require non-local knowledge to determine that they are correct). Code _should_ always use arrow functions to call instance methods (`const handler = (x) => { this.listener(x); };`), and _should not_ obtain or pass references to instance methods (`const handler = this.listener; handler(x);`).

> Note: in some specific situations, e.g. when binding functions in a template, arrow functions as properties are useful and create much more readable code. Use judgement with this rule. Also, see the [`Event Handlers`](#event-handlers) section below.

```js
// Avoid:
class DelayHandler {
  constructor() {
    // Problem: `this` is not preserved in the callback. `this` in the callback
    // will not be an instance of DelayHandler.
    setTimeout(this.patienceTracker, 5000);
  }
  private patienceTracker() {
    this.waitedPatiently = true;
  }
}
```

```js
// Avoid:
// Arrow functions usually should not be properties.
class DelayHandler {
  constructor() {
    // Bad: this code looks like it forgot to bind `this`.
    setTimeout(this.patienceTracker, 5000);
  }
  private patienceTracker = () => {
    this.waitedPatiently = true;
  }
}
```

```js
// Explicitly manage `this` at call time.
class DelayHandler {
  constructor() {
    // Use anonymous functions if possible.
    setTimeout(() => {
      this.patienceTracker();
    }, 5000);
  }
  private patienceTracker() {
    this.waitedPatiently = true;
  }
}
```

### Parameter initializers

Optional function parameters _may_ be given a default initializer to use when the argument is omitted. Initializers _must not_ have any observable side effects. Initializers _should_ be kept as simple as possible.

```js
function process(name: string, extraContext: string[] = []) {}
function activate(index = 0) {}
```

```js
// BAD: side effect of incrementing the counter
let globalCounter = 0;
function newId(index = globalCounter++) {}

// BAD: exposes shared mutable state, which can introduce unintended coupling
// between function calls
class Foo {
  private readonly defaultPaths: string[];
  frobnicate(paths = defaultPaths) {}
}
```

Use default parameters sparingly. Prefer [destructuring](#object-destructuring) to create readable APIs when there are more than a small handful of optional parameters that do not have a natural order.

### Prefer rest and spread when appropriate

Use a _rest_ parameter instead of accessing `arguments`. Never name a local variable or parameter `arguments`, which confusingly shadows the built-in name.

```js
function variadic(array: string[], ...numbers: number[]) {}
```

Use function spread syntax instead of `Function.prototype.apply`.

### Formatting functions

- Blank lines at the start or end of the function body are not allowed.
- A single blank line _may_ be used within function bodies sparingly to create _logical groupings_ of statements.
- Generators should attach the `*` to the `function` and `yield` keywords, as in `function* foo()` and `yield* iter`, rather than `function *foo()` or `yield *iter`.
- Parentheses around the left-hand side of a single-argument arrow function are recommended but not required.
- Do not put a space after the `...` in rest or spread syntax.

```js
function myFunction(...elements: number[]) {}
myFunction(...array, ...iterable, ...generator());
```

## Primitive literals

### String literals

#### Use single quotes

Ordinary string literals are delimited with single quotes (`'`), rather than double quotes (`"`).

> **Tip**
> If a string contains a single quote character, consider using a template string to avoid having to escape the quote.

#### No line continuations

Do not use _line continuations_ (that is, ending a line inside a string literal with a backslash) in either ordinary or template string literals. Even though ES5 allows this, it can lead to tricky errors if any trailing whitespace comes after the slash, and is less obvious to readers.

Disallowed:

```js
const LONG_STRING =
  'This is a very very very very very very very long string. \
        It inadvertently contains long stretches of spaces due to how the \
        continued lines are indented.';
```

Instead, write

```js
const LONG_STRING =
  'This is a very very very very very very long string. ' +
  'It does not contain long stretches of spaces because it uses ' +
  'concatenated strings.';
const SINGLE_STRING =
  'http://it.is.also/acceptable_to_use_a_single_long_string_when_breaking_would_hinder_search_discoverability';
```

#### Template literals

Use template literals (delimited with `` ` ``) over complex string concatenation, particularly if multiple string literals are involved. Template literals may span multiple lines.

If a template literal spans multiple lines, it does not need to follow the indentation of the enclosing block, though it may if the added whitespace does not matter.

Example:

```js
function arithmetic(a: number, b: number) {
  return `Here is a table of arithmetic operations:
    ${a} + ${b} = ${a + b}
    ${a} - ${b} = ${a - b}
    ${a} * ${b} = ${a * b}
    ${a} / ${b} = ${a / b}`;
}
```

### Number literals

Numbers may be specified in decimal, hex, octal, or binary. Use exactly `0x`, `0o`, and `0b` prefixes, with lowercase letters, for hex, octal, and binary, respectively. Never include a leading zero unless it is immediately followed by `x`, `o`, or `b`.

### Type coercion

TypeScript code _may_ use the `String()` and `Boolean()` (note: no `new`!) functions, string template literals, or `!!` to coerce types.

```ts
const bool = Boolean(false);
const str = String(aNumber);
const bool2 = !!str;
const str2 = `result: ${bool2}`;
```

Values of enum types (including unions of enum types and other types) _must not_ be converted to booleans with `Boolean()` or `!!`, and must instead be compared explicitly with comparison operators.

```ts
// Avoid
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
let enabled = Boolean(level);

const maybeLevel: SupportLevel|undefined = ...;
enabled = !!maybeLevel;
```

```ts
// Prefer
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
let enabled = level !== SupportLevel.NONE;

const maybeLevel: SupportLevel|undefined = ...;
enabled = level !== undefined && level !== SupportLevel.NONE;
```

Why?

For most purposes, it doesn't matter what number or string value an enum name is mapped to at runtime, because values of enum types are referred to by name in source code. Consequently, engineers are accustomed to not thinking about this, and so situations where it _does_ matter are undesirable because they will be surprising. Such is the case with conversion of enums to booleans; in particular, by default, the first declared enum value is falsy (because it is 0) while the others are truthy, which is likely to be unexpected. Readers of code that uses an enum value may not even know whether it's the first declared value or not.

Using string concatenation to cast to string is discouraged, as we check that operands to the plus operator are of matching types.

Code _must_ use `Number()` to parse numeric values, and _must_ check its return for `NaN` values explicitly, unless failing to parse is impossible from context.

> **Note** > `Number('')`, `Number(' ')`, and `Number('\t')` would return `0` instead of `NaN`. `Number('Infinity')` and `Number('-Infinity')` would return `Infinity` and `-Infinity` respectively. Additionally, exponential notation such as `Number('1e+309')` and `Number('-1e+309')` can overflow into `Infinity`. These cases may require special handling.

```js
const aNumber = Number('123');
if (!isFinite(aNumber)) throw new Error(...);
```

Code _must not_ use unary plus (`+`) to coerce strings to numbers. Parsing numbers can fail, has surprising corner cases, and can be a code smell (parsing at the wrong layer). A unary plus is too easy to miss in code reviews given this.

```js
// Avoid
const x = +y;
```

Code also _must not_ use `parseInt` or `parseFloat` to parse numbers, except for non-base-10 strings (see below). Both of those functions ignore trailing characters in the string, which can shadow error conditions (e.g. parsing `12 dwarves` as `12`).

```js
// Avoid
const n = parseInt(someString, 10); // Error prone,
const f = parseFloat(someString); // regardless of passing a radix.
```

Code that requires parsing with a radix _must_ check that its input contains only appropriate digits for that radix before calling into `parseInt`;

```js
if (!/^[a-fA-F0-9]+$/.test(someString)) throw new Error(...);
// Needed to parse hexadecimal.
// tslint:disable-next-line:ban
const n = parseInt(someString, 16);  // Only allowed for radix != 10
```

Use `Number()` followed by `Math.floor` or `Math.trunc` (where available) to parse integer numbers:

```js
let f = Number(someString);
if (isNaN(f)) handleError();
f = Math.floor(f);
```

#### Implicit coercion

Do not use explicit boolean coercions in conditional clauses that have implicit boolean coercion. Those are the conditions in an `if`, `for` and `while` statements.

```js
// Avoid
const foo: MyInterface|null = ...;
if (!!foo) {...}
while (!!foo) {...}
```

```js
// Prefer
const foo: MyInterface|null = ...;
if (foo) {...}
while (foo) {...}
```

[As with explicit conversions](#type-coercion), values of enum types (including unions of enum types and other types) _must not_ be implicitly coerced to booleans, and must instead be compared explicitly with comparison operators.

```ts
// Avoid
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
if (level) {...}

const maybeLevel: SupportLevel|undefined = ...;
if (level) {...}
```

```ts
// Prefer
enum SupportLevel {
  NONE,
  BASIC,
  ADVANCED,
}

const level: SupportLevel = ...;
if (level !== SupportLevel.NONE) {...}

const maybeLevel: SupportLevel|undefined = ...;
if (level !== undefined && level !== SupportLevel.NONE) {...}
```

Other types of values may be either implicitly coerced to booleans or compared explicitly with comparison operators:

```js
// Explicitly comparing > 0 is OK:
if (arr.length > 0) {...}
// so is relying on boolean coercion:
if (arr.length) {...}
```

## Control structures

### Control flow statements and blocks

Control flow statements (`if`, `else`, `for`, `do`, `while`, etc) **always** use braced blocks for the containing code, **even if the body contains only a single statement**. The first statement of a non-empty block must begin on its own line.

```js
// Do
for (let i = 0; i < x; i++) {
  doSomethingWith(i);
}

if (x) {
  doSomethingWithALongMethodNameThatForcesANewLine(x);
}
```

```js
// Must not
if (x)
  doSomethingWithAnExtremelyAndRidiculousLongMethodNameThatInnevitablyForcesANewLine(
    x
  );

for (let i = 0; i < x; i++) doSomethingWith(i);
```

**Exception:** `if` statements fitting on one line _may_ elide the block. But prefer using braced blocks when possible.

```js
if (x) x.doFoo();
```

#### Assignment in control statements

Prefer to avoid assignment of variables inside control statements. Assignment can be easily mistaken for equality checks inside control statements.

```js
// Must not
if ((x = someFunction())) {
  // Assignment easily mistaken with equality check
  // ...
}
```

```js
// Do
x = someFunction();
if (x) {
  // ...
}
```

#### Iterating containers

Prefer `for (... of someArr)` to iterate over arrays. `Array.prototype.forEach` and vanilla `for` loops are also allowed:

```js
for (const x of someArr) {
  // x is a value of someArr.
}

for (let i = 0; i < someArr.length; i++) {
  // Explicitly count if the index is needed, otherwise use the for/of form.
  const x = someArr[i];
  // ...
}
for (const [i, x] of someArr.entries()) {
  // Alternative version of the above.
}
```

`for`\-`in` loops may only be used on dict-style objects. Do not use `for (... in ...)` to iterate over arrays as it will counterintuitively give the array's indices (as strings!), not values:

```js
// Avoid
for (const x in someArray) {
  // x is the index!
}
```

`Object.prototype.hasOwnProperty` should be used in `for`\-`in` loops to exclude unwanted prototype properties. Prefer `for`\-`of` with `Object.keys`, `Object.values`, or `Object.entries` over `for`\-`in` when possible.

```js
for (const key in obj) {
  if (!obj.hasOwnProperty(key)) continue;
  doWork(key, obj[key]);
}
for (const key of Object.keys(obj)) {
  doWork(key, obj[key]);
}
for (const value of Object.values(obj)) {
  doWorkValOnly(value);
}
for (const [key, value] of Object.entries(obj)) {
  doWork(key, value);
}
```

### Grouping parentheses

Optional grouping parentheses are omitted only when the author and reviewer agree that there is no reasonable chance that the code will be misinterpreted without them, nor would they have made the code easier to read. It is _not_ reasonable to assume that every reader has the entire operator precedence table memorized.

Do not use unnecessary parentheses around the entire expression following `delete`, `typeof`, `void`, `return`, `throw`, `case`, `in`, `of`, or `yield`.

### Exception handling

Exceptions are an important part of the language and should be used whenever exceptional cases occur.

Custom exceptions provide a great way to convey additional error information from functions. They should be defined and used wherever the native `Error` type is insufficient.

Prefer throwing exceptions over ad-hoc error-handling approaches (such as passing an error container reference type, or returning an object with an error property).

#### Instantiate errors using `new`

Always use `new Error()` when instantiating exceptions, instead of just calling `Error()`. Both forms create a new `Error` instance, but using `new` is more consistent with how other objects are instantiated.

```js
// Do
throw new Error('Foo is not a valid bar.');
```

```js
// Must not
throw Error('Foo is not a valid bar.');
```

#### Only throw errors

JavaScript (and thus TypeScript) allow throwing or rejecting a Promise with arbitrary values. However if the thrown or rejected value is not an `Error`, it does not populate stack trace information, making debugging hard. This treatment extends to `Promise` rejection values as `Promise.reject(obj)` is equivalent to `throw obj;` in async functions.

```js
// Must not
// bad: does not get a stack trace.
throw 'oh noes!';
// For promises
new Promise((resolve, reject) => void reject('oh noes!'));
Promise.reject();
Promise.reject('oh noes!');
```

Instead, only throw (subclasses of) `Error`:

```js
// Do
// Throw only Errors
throw new Error('oh noes!');
// ... or subtypes of Error.
class MyError extends Error {}
throw new MyError('my oh noes!');
// For promises
new Promise(resolve => resolve()); // No reject is OK.
new Promise((resolve, reject) => void reject(new Error('oh noes!')));
Promise.reject(new Error('oh noes!'));
```

#### Catching and rethrowing

When catching errors, code _should_ assume that all thrown errors are instances of `Error`.

```js
function assertIsError(e: unknown): asserts e is Error {
  if (!(e instanceof Error)) throw new Error("e is not an Error");
}

try {
  doSomething();
} catch (e: unknown) {
  // All thrown errors must be Error subtypes. Do not handle
  // other possible values unless you know they are thrown.
  assertIsError(e);
  displayError(e.message);
  // or rethrow:
  throw e;
}
```

Exception handlers _must not_ defensively handle non-`Error` types unless the called API is conclusively known to throw non-`Error`s in violation of the above rule. In that case, a comment should be included to specifically identify where the non-`Error`s originate.

```js
try {
  badApiThrowingStrings();
} catch (e: unknown) {
  // Note: bad API throws strings instead of errors.
  if (typeof e === 'string') { ... }
}
```

Why?

Avoid [overly defensive programming](https://en.wikipedia.org/wiki/Defensive_programming#Offensive_programming). Repeating the same defenses against a problem that will not exist in most code leads to boiler-plate code that is not useful.

#### Empty catch blocks

It is very rarely correct to do nothing in response to a caught exception. When it truly is appropriate to take no action whatsoever in a catch block, the reason this is justified is explained in a comment.

```js
  try {
    return handleNumericResponse(response);
  } catch (e: unknown) {
    // Response is not numeric. Continue to handle as text.
  }
  return handleTextResponse(response);
```

Disallowed:

```js
  try {
    shouldFail();
    fail('expected an error');
  } catch (expected: unknown) {
  }
```

> **Tip**
> Unlike in some other languages, patterns like the above simply don’t work since this will catch the error thrown by `fail`. Use `assertThrows()` instead.

### Switch statements

All `switch` statements _must_ contain a `default` statement group, even if it contains no code. The `default` statement group must be last.

```js
switch (x) {
  case Y:
    doSomethingElse();
    break;
  default:
  // nothing to do.
}
```

Within a switch block, each statement group either terminates abruptly with a `break`, a `return` statement, or by throwing an exception. Non-empty statement groups (`case ...`) _must not_ fall through (enforced by the compiler):

```js
// Avoid
switch (x) {
  case X:
    doSomething();
  // fall through - not allowed!
  case Y:
  // ...
}
```

Empty statement groups are allowed to fall through:

```js
switch (x) {
  case X:
  case Y:
    doSomething();
    break;
  default: // nothing to do.
}
```

### Equality checks

**Always** use triple equals (`===`) and not equals (`!==`). The double equality operators cause error prone type coercions that are hard to understand and slower to implement for JavaScript Virtual Machines. See also the [JavaScript equality table](https://dorey.github.io/JavaScript-Equality-Table/).

```js
// Must not
if (foo == 'bar' || baz != bam) {
  // Hard to understand behaviour due to type coercion.
}
```

```js
// Do
if (foo === 'bar' || baz !== bam) {
  // All good here.
}
```

**Exception:** Comparisons to the literal `null` value _may_ use the `==` and `!=` operators to cover both `null` and `undefined` values.

```js
if (foo == null) {
  // Will trigger when foo is null or undefined.
}
```

### Type and non-nullability assertions

Type assertions (`x as SomeType`) and non-nullability assertions (`y!`) are unsafe. Both only silence the TypeScript compiler, but do not insert any runtime checks to match these assertions, so they can cause your program to crash at runtime.

Because of this, you _should not_ use type and non-nullability assertions without an obvious or explicit reason for doing so.

Instead of the following:

```ts
// Avoid
(x as Foo).foo();
y!.bar();
```

When you want to assert a type or non-nullability the best answer is to explicitly write a runtime check that performs that check.

```js
// assuming Foo is a class.
if (x instanceof Foo) {
  x.foo();
}

if (y) {
  y.bar();
}
```

Sometimes due to some local property of your code you can be sure that the assertion form is safe. In those situations, you _should_ add clarification to explain why you are ok with the unsafe behavior:

```js
// x is a Foo, because ...
(x as Foo).foo();

// y cannot be null, because ...
y!.bar();
```

If the reasoning behind a type or non-nullability assertion is obvious, the comments _may_ not be necessary. For example, generated proto code is always nullable, but perhaps it is well-known in the context of the code that certain fields are always provided by the backend. Use your judgement.

#### Type assertion syntax

Type assertions _must_ use the `as` syntax (as opposed to the angle brackets syntax). This enforces parentheses around the assertion when accessing a member.

```ts
// Must not
const x = (<Foo>z).length;
const y = <Foo>z.length;
```

```ts
// Do
// z must be Foo because ...
const x = (z as Foo).length;
```

#### Double assertions

From the [TypeScript handbook](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-assertions), TypeScript only allows type assertions which convert to a _more specific_ or _less specific_ version of a type. Adding a type assertion (`x as Foo`) which does not meet this criteria will give the error: Conversion of type 'X' to type 'Y' may be a mistake because neither type sufficiently overlaps with the other.

If you are sure an assertion is safe, you can perform a _double assertion_. This involves casting through `unknown` since it is less specific than all types.

```ts
// x is a Foo here, because...
(x as unknown as Foo).fooMethod();
```

Use `unknown` (instead of `any` or `{}`) as the intermediate type.

#### Type assertions and object literals

Use type annotations (`: Foo`) instead of type assertions (`as Foo`) to specify the type of an object literal. This allows detecting refactoring bugs when the fields of an interface change over time.

```ts
// Avoid
interface Foo {
  bar: number;
  baz?: string; // was "bam", but later renamed to "baz".
}

const foo = {
  bar: 123,
  bam: 'abc', // no error!
} as Foo;

function func() {
  return {
    bar: 123,
    bam: 'abc', // no error!
  } as Foo;
}
```

```ts
// Prefer
interface Foo {
  bar: number;
  baz?: string;
}

const foo: Foo = {
  bar: 123,
  bam: 'abc', // complains about "bam" not being defined on Foo.
};

function func(): Foo {
  return {
    bar: 123,
    bam: 'abc', // complains about "bam" not being defined on Foo.
  };
}
```

### Keep try blocks focused

Limit the amount of code inside a try block, if this can be done without hurting readability.

```js
// Avoid
try {
  const result = methodThatMayThrow();
  use(result);
} catch (error: unknown) {
  // ...
}
```

```js
// Prefer
let result;
try {
  result = methodThatMayThrow();
} catch (error: unknown) {
  // ...
}
use(result);
```

Moving the non-throwable lines out of the try/catch block helps the reader learn which method throws exceptions. Some inline calls that do not throw exceptions could stay inside because they might not be worth the extra complication of a temporary variable.

**Exception:** There may be performance issues if try blocks are inside a loop. Widening try blocks to cover a whole loop is ok.

# Naming

## Identifiers

Identifiers _must_ use only ASCII letters, digits, underscores (for constants and structured test method names), and (rarely) the '$' sign.

### Naming style

TypeScript expresses information in types, so names _should not_ be decorated with information that is included in the type. (See also [Testing Blog](https://testing.googleblog.com/2017/10/code-health-identifiernamingpostforworl.html) for more about what not to include.)

Some concrete examples of this rule:

- Do not use trailing or leading underscores for private properties or methods.
- Do not use the `opt_` prefix for optional parameters.
  - For accessors, see [accessor rules](#getters-and-setters-accessors) below.
- Do not mark interfaces specially (~~`IMyInterface`~~ or ~~`MyFooInterface`~~) unless it's idiomatic in its environment. When introducing an interface for a class, give it a name that expresses why the interface exists in the first place (e.g. `class TodoItem` and `interface TodoItemStorage` if the interface expresses the format used for storage/serialization in JSON).
- Suffixing `Observable`s with `$` is a common external convention and can help resolve confusion regarding observable values vs concrete values. Judgement on whether this is a useful convention is left up to individual teams, but _should_ be consistent within projects.

### Descriptive names

Names **_must_** be descriptive and clear to a new reader. Do not use abbreviations that are ambiguous or unfamiliar to readers outside your project, and do not abbreviate by deleting letters within a word.

- **Exception:** Variables that are in scope for 10 lines or fewer, including arguments that are _not_ part of an exported API, _may_ use short (e.g. single letter) variable names.

```js
// Good identifiers:
errorCount; // No abbreviation.
dnsConnectionIndex; // Most people know what "DNS" stands for.
referrerUrl; // Ditto for "URL".
customerId; // "Id" is both ubiquitous and unlikely to be misunderstood.
```

```js
// Disallowed identifiers:
n; // Meaningless.
nErr; // Ambiguous abbreviation.
nCompConns; // Ambiguous abbreviation.
wgcConnections; // Only your group knows what this stands for.
pcReader; // Lots of things can be abbreviated "pc".
cstmrId; // Deletes internal letters.
kSecondsPerDay; // Do not use Hungarian notation.
customerID; // Incorrect camelcase of "ID".
```

### Camel case

Treat abbreviations like acronyms in names as whole words, i.e. use `loadHttpUrl`, not ~~`loadHTTPURL`~~, unless required by a platform name (e.g. `XMLHttpRequest`).

### Dollar sign

Identifiers _should not_ generally use `$`, except when required by naming conventions for third party frameworks. [See above](#naming-style) for more on using `$` with `Observable` values.

## Rules by identifier type

Most identifier names should follow the casing in the table below, based on the identifier's type.

| Style            | Category                                                                                                               |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `UpperCamelCase` | class / interface / type / enum / decorator / type parameters / component functions in TSX / JSXElement type parameter |
| `lowerCamelCase` | variable / parameter / function / method / property / module alias                                                     |
| `CONSTANT_CASE`  | global constant values, including enum values. See Constants below.                                                    |
| `#ident`         | private identifiers are never used.                                                                                    |

### Type parameters

Type parameters, like in `Array<T>`, _may_ use a single upper case character (`T`) or `UpperCamelCase`.

### Test names

Test method names inxUnit-style test frameworks _may_ be structured with `_` separators, e.g. `testX_whenY_doesZ()`.

### `_` prefix/suffix

Identifiers must not use `_` as a prefix or suffix.

This also means that `_` _must not_ be used as an identifier by itself (e.g. to indicate a parameter is unused).

> Tip: If you only need some of the elements from an array (or TypeScript tuple), you can insert extra commas in a destructuring statement to ignore in-between elements:
>
>     const [a, , b] = [1, 5, 10];  // a <- 1, b <- 10

### Imports

Module namespace imports are `lowerCamelCase` while files are `snake_case`, which means that imports correctly will not match in casing style, such as

```js
import * as fooBar from './foo_bar';
```

Some libraries might commonly use a namespace import prefix that violates this naming scheme, but overbearingly common open source use makes the violating style more readable. The only libraries that currently fall under this exception are:

- [jquery](https://jquery.com/), using the `$` prefix
- [threejs](https://threejs.org/), using the `THREE` prefix

### Constants

**Immutable**: `CONSTANT_CASE` indicates that a value is _intended_ to not be changed, and _may_ be used for values that can technically be modified (i.e. values that are not deeply frozen) to indicate to users that they must not be modified.

```js
const UNIT_SUFFIXES = {
  milliseconds: 'ms',
  seconds: 's',
};
// Even though per the rules of JavaScript UNIT_SUFFIXES is
// mutable, the uppercase shows users to not modify it.
```

A constant can also be a `static readonly` property of a class.

```ts
class Foo {
  private static readonly MY_SPECIAL_NUMBER = 5;

  bar() {
    return 2 * Foo.MY_SPECIAL_NUMBER;
  }
}
```

**Global**: Only symbols declared on the module level, static fields of module level classes, and values of module level enums, _may_ use `CONST_CASE`. If a value can be instantiated more than once over the lifetime of the program (e.g. a local variable declared within a function, or a static field on a class nested in a function) then it _must_ use `lowerCamelCase`.

If a value is an arrow function that implements an interface, then it _may_ be declared `lowerCamelCase`.

### Aliases

When creating a local-scope alias of an existing symbol, use the format of the existing identifier. The local alias _must_ match the existing naming and format of the source. For variables use `const` for your local aliases, and for class fields use the `readonly` attribute.

> Note: If you're creating an alias just to expose it to a template in your framework of choice, remember to also apply the proper [access modifiers](#properties-used-outside-of-class-lexical-scope).

```ts
const { BrewStateEnum } = SomeType;
const CAPACITY = 5;

class Teapot {
  readonly BrewStateEnum = BrewStateEnum;
  readonly CAPACITY = CAPACITY;
}
```

# Type system

## Type inference

Code _may_ rely on type inference as implemented by the TypeScript compiler for all type expressions (variables, fields, return types, etc).

```ts
const x = 15; // Type inferred.
```

Leave out type annotations for trivially inferred types: variables or parameters initialized to a `string`, `number`, `boolean`, `RegExp` literal or `new` expression.

```ts
// Avoid
const x: boolean = true; // Bad: 'boolean' here does not aid readability

// Bad: 'Set' is trivially inferred from the initialization
const x: Set<string> = new Set();
```

Explicitly specifying types may be required to prevent generic type parameters from being inferred as `unknown`. For example, initializing generic types with no values (e.g. empty arrays, objects, `Map`s, or `Set`s).

```ts
// Prefer
const x = new Set<string>();
```

For more complex expressions, type annotations can help with readability of the program:

```ts
// Avoid
// Hard to reason about the type of 'value' without an annotation.
const value = await rpc.getSomeValue().transform();
```

```ts
// Prefer
// Can tell the type of 'value' at a glance.
const value: string[] = await rpc.getSomeValue().transform();
```

Whether an annotation is required is decided by the code reviewer. But should always be preferred anyway.

### Return types

Whether to include return type annotations for functions and methods is up to the code author. Reviewers _may_ ask for annotations to clarify complex return types that are hard to understand. Projects _may_ have a local policy to always require return types, but this is not a general TypeScript style requirement.

There are two benefits to explicitly typing out the implicit return values of functions and methods:

- More precise documentation to benefit readers of the code.
- Surface potential type errors faster in the future if there are code changes that change the return type of the function.

## Undefined and null

TypeScript supports `undefined` and `null` types. Nullable types can be constructed as a union type (`string|null`); similarly with `undefined`. There is no special syntax for unions of `undefined` and `null`.

TypeScript code can use either `undefined` or `null` to denote absence of a value, there is no general guidance to prefer one over the other. Many JavaScript APIs use `undefined` (e.g. `Map.get`), while many DOM and APIs use `null` (e.g. `Element.getAttribute`), so the appropriate absent value depends on the context.

### Nullable/undefined type aliases

Type aliases _must not_ include `|null` or `|undefined` in a union type. Nullable aliases typically indicate that null values are being passed around through too many layers of an application, and this clouds the source of the original issue that resulted in `null`. They also make it unclear when specific values on a class or interface might be absent.

Instead, code _must_ only add `|null` or `|undefined` when the alias is actually used. Code _should_ deal with null values close to where they arise, using the above techniques.

```ts
// Bad
type CoffeeResponse = Latte|Americano|undefined;

class CoffeeService {
  getLatte(): CoffeeResponse { ... };
}
```

```ts
// Better
type CoffeeResponse = Latte|Americano;

class CoffeeService {
  getLatte(): CoffeeResponse|undefined { ... };
}
```

### Prefer optional over `|undefined`

In addition, TypeScript supports a special construct for optional parameters and fields, using `?`:

```ts
interface CoffeeOrder {
  sugarCubes: number;
  milk?: Whole|LowFat|HalfHalf;
}

function pourCoffee(volume?: Milliliter) { ... }
```

Optional parameters implicitly include `|undefined` in their type. However, they are different in that they can be left out when constructing a value or calling a method. For example, `{sugarCubes: 1}` is a valid `CoffeeOrder` because `milk` is optional.

Use optional fields (on interfaces or classes) and parameters rather than a `|undefined` type.

For classes preferably avoid this pattern altogether and initialize as many fields as possible.

```ts
class MyClass {
  field = '';
}
```

## Use structural types

TypeScript's type system is structural, not nominal. That is, a value matches a type if it has at least all the properties the type requires and the properties' types match, recursively.

When providing a structural-based implementation, explicitly include the type at the declaration of the symbol (this allows more precise type checking and error reporting).

```ts
// Prefer
const foo: Foo = {
  a: 123,
  b: 'abc',
};
```

```ts
// Avoid
const badFoo = {
  a: 123,
  b: 'abc',
};
```

Use interfaces to define structural types, not classes

```ts
// Prefer
interface Foo {
  a: number;
  b: string;
}

const foo: Foo = {
  a: 123,
  b: 'abc',
};
```

```ts
// Avoid
class Foo {
  readonly a: number;
  readonly b: number;
}

const foo: Foo = {
  a: 123,
  b: 'abc',
};
```

Why?

The badFoo object above relies on type inference. Additional fields could be added to badFoo and the type is inferred based on the object itself.

When passing a badFoo to a function that takes a Foo, the error will be at the function call site, rather than at the object declaration site. This is also useful when changing the surface of an interface across broad codebases.

```ts
interface Animal {
  sound: string;
  name: string;
}

function makeSound(animal: Animal) {}

/**
 * 'cat' has an inferred type of '{sound: string}'
 */
const cat = {
  sound: 'meow',
};

/**
 * 'cat' does not meet the type contract required for the function, so the
 * TypeScript compiler errors here, which may be very far from where 'cat' is
 * defined.
 */
makeSound(cat);

/**
 * Horse has a structural type and the type error shows here rather than the
 * function call.  'horse' does not meet the type contract of 'Animal'.
 */
const horse: Animal = {
  sound: 'niegh',
};

const dog: Animal = {
  sound: 'bark',
  name: 'MrPickles',
};

makeSound(dog);
makeSound(horse);
```

## Prefer interfaces over type literal aliases

TypeScript supports [type aliases](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#type-aliases) for naming a type expression. This can be used to name primitives, unions, tuples, and any other types.

However, when declaring types for objects, use interfaces instead of a type alias for the object literal expression.

```ts
// Prefer
interface User {
  firstName: string;
  lastName: string;
}
```

```ts
// Avoid
type User = {
  firstName: string;
  lastName: string;
};
```

Why?

These forms are nearly equivalent, so under the principle of just choosing one out of two forms to prevent variation, we should choose one. Additionally, there are also [interesting technical reasons to prefer interface](https://ncjamieson.com/prefer-interfaces/). That page quotes the TypeScript team lead: Honestly, my take is that it should really just be interfaces for anything that they can model. There is no benefit to type aliases when there are so many issues around display/perf.

## `Array<T>` Type

For simple types (containing just alphanumeric characters and dot), use the syntax sugar for arrays, `T[]` or `readonly T[]`, rather than the longer form `Array<T>` or `ReadonlyArray<T>`.

For multi-dimensional non-`readonly` arrays of simple types, use the syntax sugar form (`T[][]`, `T[][][]`, and so on) rather than the longer form.

For anything more complex, use the longer form `Array<T>`.

These rules apply at each level of nesting, i.e. a simple `T[]` nested in a more complex type would still be spelled as `T[]`, using the syntax sugar.

```ts
// Prefer
let a: string[];
let b: readonly string[];
let c: ns.MyObj[];
let d: string[][];
let e: Array<{ n: number; s: string }>;
let f: Array<string | number>;
let g: ReadonlyArray<string | number>;
let h: InjectionToken<string[]>; // Use syntax sugar for nested types.
let i: ReadonlyArray<string[]>;
let j: Array<readonly string[]>;
```

```ts
// Avoid
let a: Array<string>; // The syntax sugar is shorter.
let b: ReadonlyArray<string>;
let c: Array<ns.MyObj>;
let d: Array<string[]>;
let e: { n: number; s: string }[]; // The braces make it harder to read.
let f: (string | number)[]; // Likewise with parens.
let g: readonly (string | number)[];
let h: InjectionToken<Array<string>>;
let i: readonly string[][];
let j: (readonly string[])[];
```

## Indexable types / index signatures (`{[key: string]: T}`)

In JavaScript, it's common to use an object as an associative array (aka map, hash, or dict). Such objects can be typed using an [index signature](https://www.typescriptlang.org/docs/handbook/2/objects.html#index-signatures) (`[k: string]: T`) in TypeScript:

```ts
const fileSizes: { [fileName: string]: number } = {};
fileSizes['readme.txt'] = 541;
```

In TypeScript, provide a meaningful label for the key. (The label only exists for documentation; it's unused otherwise.)

```ts
// Avoid
const users: {[key: string]: number} = ...;
```

```ts
// Prefer
const users: {[userName: string]: number} = ...;
```

> Rather than using one of these, consider using the ES6 `Map` and `Set` types instead. JavaScript objects have [surprising undesirable behaviors](http://2ality.com/2012/01/objects-as-maps.html) and the ES6 types more explicitly convey your intent. Also, `Map`s can be keyed by—and `Set`s can contain—types other than `string`.

TypeScript's builtin `Record<Keys, ValueType>` type allows constructing types with a defined set of keys. This is distinct from associative arrays in that the keys are statically known. See advice on that [below](#mapped-conditional-types).

## Mapped and conditional types

TypeScript's [mapped types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) and [conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html) allow specifying new types based on other types. TypeScript's standard library includes several type operators based on these (`Record`, `Partial`, `Readonly` etc).

These type system features allow succinctly specifying types and constructing powerful yet type safe abstractions. They come with a number of drawbacks though:

- Compared to explicitly specifying properties and type relations (e.g. using interfaces and extension, see below for an example), type operators require the reader to mentally evaluate the type expression. This can make programs substantially harder to read, in particular combined with type inference and expressions crossing file boundaries.
- Mapped & conditional types' evaluation model, in particular when combined with type inference, is underspecified, not always well understood, and often subject to change in TypeScript compiler versions. Code can accidentally compile or seem to give the right results. This increases future support cost of code using type operators.
- Mapped & conditional types are most powerful when deriving types from complex and/or inferred types. On the flip side, this is also when they are most prone to create hard to understand and maintain programs.
- Some language tooling does not work well with these type system features. E.g. your IDE's find references (and thus rename property refactoring) will not find properties in a `Pick<T, Keys>` type, and Code Search won't hyperlink them.

The style recommendation is:

- Always use the simplest type construct that can possibly express your code.
- A little bit of repetition or verbosity is often much cheaper than the long term cost of complex type expressions.
- Mapped & conditional types may be used, subject to these considerations.

For example, TypeScript's builtin `Pick<T, Keys>` type allows creating a new type by subsetting another type `T`, but simple interface extension can often be easier to understand.

```ts
interface User {
  shoeSize: number;
  favoriteIcecream: string;
  favoriteChocolate: string;
}

// FoodPreferences has favoriteIcecream and favoriteChocolate, but not shoeSize.
type FoodPreferences = Pick<User, 'favoriteIcecream' | 'favoriteChocolate'>;
```

This is equivalent to spelling out the properties on `FoodPreferences`:

```ts
interface FoodPreferences {
  favoriteIcecream: string;
  favoriteChocolate: string;
}
```

To reduce duplication, `User` could extend `FoodPreferences`, or (possibly better) nest a field for food preferences:

```ts
interface FoodPreferences {
  /* as above */
}
interface User extends FoodPreferences {
  shoeSize: number;
  // also includes the preferences.
}
```

Using interfaces here makes the grouping of properties explicit, improves IDE support, allows better optimization, and arguably makes the code easier to understand.

## `any` Type

TypeScript's `any` type is a super and subtype of all other types, and allows dereferencing all properties. As such, `any` is dangerous - it can mask severe programming errors, and its use undermines the value of having static types in the first place.

**Consider _not_ to use `any`.** In circumstances where you want to use `any`, consider one of:

- [Provide a more specific type](#providing-a-more-specific-type)
- [Use `unknown`](#using-unknown-over-any)
- [Suppress the lint warning and document why](#suppressing-any-lint-warnings)

### Providing a more specific type

Use interfaces , an inline object type, or a type alias:

```ts
// Use declared interfaces to represent server-side JSON.
declare interface MyUserJson {
  name: string;
  email: string;
}

// Use type aliases for types that are repetitive to write.
type MyType = number | string;

// Or use inline object types for complex returns.
function getTwoThings(): { something: number; other: string } {
  // ...
  return { something, other };
}

// Use a generic type, where otherwise a library would say `any` to represent
// they don't care what type the user is operating on (but note "Return type
// only generics" below).
function nicestElement<T>(items: T[]): T {
  // Find the nicest element in items.
  // Code can also put constraints on T, e.g. <T extends HTMLElement>.
}
```

### Using `unknown` over `any`

The `any` type allows assignment into any other type and dereferencing any property off it. Often this behaviour is not necessary or desirable, and code just needs to express that a type is unknown. Use the built-in type `unknown` in that situation — it expresses the concept and is much safer as it does not allow dereferencing arbitrary properties.

```ts
// Prefer
// Can assign any value (including null or undefined) into this but cannot
// use it without narrowing the type or casting.
const val: unknown = value;
```

```ts
// Avoid
const danger: any = value; /* result of an arbitrary expression */
danger.whoops(); // This access is completely unchecked!
```

To safely use `unknown` values, narrow the type using a [type guard](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types)

### Suppressing `any` lint warnings

Sometimes using `any` is legitimate, for example in tests to construct a mock object. In such cases, add a comment that suppresses the lint warning, and document why it is legitimate.

```ts
// This test only needs a partial implementation of BookService, and if
// we overlooked something the test will fail in an obvious way.
// This is an intentionally unsafe partial mock
// tslint:disable-next-line:no-any
const mockBookService = {
  get() {
    return mockBook;
  },
} as any as BookService;
// Shopping cart is not used in this test
// tslint:disable-next-line:no-any
const component = new MyComponent(
  mockBookService,
  /* unused ShoppingCart */ null as any
);
```

## `{}` Type

The `{}` type, also known as an _empty interface_ type, represents a interface with no properties. An empty interface type has no specified properties and therefore any non-nullish value is assignable to it.

```ts
let player: {};

player = {
  health: 50,
}; // Allowed.

console.log(player.health); // Property 'health' does not exist on type '{}'.
```

```ts
function takeAnything(obj: {}) {}

takeAnything({});
takeAnything({ a: 1, b: 2 });
```

Code **should not** use `{}` for most use cases. `{}` represents any non-nullish primitive or object type, which is rarely appropriate. Prefer one of the following more-descriptive types:

- `unknown` can hold any value, including `null` or `undefined`, and is generally more appropriate for opaque values.
- `Record<string, T>` is better for dictionary-like objects, and provides better type safety by being explicit about the type `T` of contained values (which may itself be `unknown`).
- `object` excludes primitives as well, leaving only non-nullish functions and objects, but without any other assumptions about what properties may be available.

## Tuple types

If you are tempted to create a Pair type, instead use a tuple type:

```ts
// Avoid
interface Pair {
  first: string;
  second: string;
}
function splitInHalf(input: string): Pair {
  ...
  return {first: x, second: y};
}
```

```ts
// Prefer
function splitInHalf(input: string): [string, string] {
  ...
  return [x, y];
}

// Use it like:
const [leftHalf, rightHalf] = splitInHalf('my string');
```

However, often it's clearer to provide meaningful names for the properties.

If declaring an `interface` is too heavyweight, you can use an inline object literal type:

```ts
function splitHostPort(address: string): {host: string, port: number} {
  ...
}

// Use it like:
const address = splitHostPort(userAddress);
use(address.port);

// You can also get tuple-like behavior using destructuring:
const {host, port} = splitHostPort(userAddress);
```

## Wrapper types

There are a few types related to JavaScript primitives that _should not_ ever be used:

- `String`, `Boolean`, and `Number` have slightly different meaning from the corresponding primitive types `string`, `boolean`, and `number`. Always use the lowercase version.
- `Object` has similarities to both `{}` and `object`, but is slightly looser. Use `{}` for a type that include everything except `null` and `undefined`, or lowercase `object` to further exclude the other primitive types (the three mentioned above, plus `symbol` and `bigint`).

Further, never invoke the wrapper types as constructors (with `new`).

## Return type only generics

Avoid creating APIs that have return type only generics. When working with existing APIs that have return type only generics always explicitly specify the generics.

# Comments and documentation

### JSDoc versus comments

There are two types of comments, JSDoc (`/** ... */`) and non-JSDoc ordinary comments (`// ...` or `/* ... */`).

- Use `/** JSDoc */` comments for documentation, i.e. comments a user of the code should read.
- Use `// line comments` for implementation comments, i.e. comments that only concern the implementation of the code itself.

JSDoc comments are understood by tools (such as editors and documentation generators), while ordinary comments are only for other humans.

### Multi-line comments

Multi-line comments are indented at the same level as the surrounding code. They _must_ use multiple single-line comments (`//`\-style), not block comment style (`/* */`).

Prefer:

```js
// This is
// fine
```

Avoid:

```js
/*
 * This should
 * use multiple
 * single-line comments
 */

/* This should use // */
```

Comments are not enclosed in boxes drawn with asterisks or other characters.

## JSDoc general form

The basic formatting of JSDoc comments is as seen in this example:

```js
/**
 * Multiple lines of JSDoc text are written here,
 * wrapped normally.
 * @param arg A number to do something to.
 */
function doSomething(arg: number) { … }
```

or in this single-line example:

```js
/** This short jsdoc describes the function. */
function doSomething(arg: number) { … }
```

If a single-line comment overflows into multiple lines, it _must_ use the multi-line style with `/**` and `*/` on their own lines.

Many tools extract metadata from JSDoc comments to perform code validation and optimization. As such, these comments _must_ be well-formed.

## Markdown

JSDoc is written in Markdown, though it _may_ include HTML when necessary.

This means that tooling parsing JSDoc will ignore plain text formatting, so if you did this:

```js
/**
 * Computes weight based on three factors:
 *   items sent
 *   items received
 *   last timestamp
 */
```

it will be rendered like this:

```
  Computes weight based on three factors: items sent items received last timestamp
```

Instead, write a Markdown list:

```js
/**
 * Computes weight based on three factors:
 *
 * - items sent
 * - items received
 * - last timestamp
 */
```

## JSDoc tags

Code style allows a subset of JSDoc tags. Most tags must occupy their own line, with the tag at the beginning of the line.

Do:

```js
/**
 * The "param" tag must occupy its own line and may not be combined.
 * @param left A description of the left param.
 * @param right A description of the right param.
 */
function add(left: number, right: number) { ... }
```

Must not:

```js
/**
 * The "param" tag must occupy its own line and may not be combined.
 * @param left @param right
 */
function add(left: number, right: number) { ... }
```

## Line wrapping

Line-wrapped block tags are indented four spaces. Wrapped description text _may_ be lined up with the description on previous lines, but this horizontal alignment is discouraged.

```js
/**
 * Illustrates line wrapping for long param/return descriptions.
 * @param foo This is a param with a particularly long description that just
 *     doesn't fit on one line.
 * @return This returns something that has a lengthy description too long to fit
 *     in one line.
 */
exports.method = function (foo) {
  return 5;
};
```

Do not indent when wrapping a `@desc` or `@fileoverview` description.

## Document all top-level exports of modules

Use `/** JSDoc */` comments to communicate information to the users of your code. Avoid merely restating the property or parameter name. You _should_ also document all properties and methods (exported/public or not) whose purpose is not immediately obvious from their name, as judged by your reviewer.

**Exception:** Symbols that are only exported to be consumed by tooling, such as @NgModule classes, do not require comments.

## Class comments

JSDoc comments for classes should provide the reader with enough information to know how and when to use the class, as well as any additional considerations necessary to correctly use the class. Textual descriptions may be omitted on the constructor.

## Method and function comments

Method, parameter, and return descriptions may be omitted if they are obvious from the rest of the method’s JSDoc or from the method name and type signature.

Method descriptions begin with a verb phrase that describes what the method does. This phrase is not an imperative sentence, but instead is written in the third person, as if there is an implied This method ... before it.

## Parameter property comments

A [parameter property](https://www.typescriptlang.org/docs/handbook/2/classes.html#parameter-properties) is a constructor parameter that is prefixed by one of the modifiers `private`, `protected`, `public`, or `readonly`. A parameter property declares both a parameter and an instance property, and implicitly assigns into it. For example, `constructor(private readonly foo: Foo)`, declares that the constructor takes a parameter `foo`, but also declares a private readonly property `foo`, and assigns the parameter into that property before executing the remainder of the constructor.

To document these fields, use JSDoc's `@param` annotation. Editors display the description on constructor calls and property accesses.

```ts
/** This class demonstrates how parameter properties are documented. */
class ParamProps {
  /**
   * @param percolator The percolator used for brewing.
   * @param beans The beans to brew.
   */
  constructor(
    private readonly percolator: Percolator,
    private readonly beans: CoffeeBean[]
  ) {}
}
```

```ts
/** This class demonstrates how ordinary fields are documented. */
class OrdinaryClass {
  /** The bean that will be used in the next call to brew(). */
  nextBean: CoffeeBean;

  constructor(initialBean: CoffeeBean) {
    this.nextBean = initialBean;
  }
}
```

## JSDoc type annotations

JSDoc type annotations are redundant in TypeScript source code. Do not declare types in `@param` or `@return` blocks, do not write `@implements`, `@enum`, `@private`, `@override` etc. on code that uses the `implements`, `enum`, `private`, `override` etc. keywords.

## Make comments that actually add information

For non-exported symbols, sometimes the name and type of the function or parameter is enough. Code will _usually_ benefit from more documentation than just variable names though!

- Avoid comments that just restate the parameter name and type, e.g.

```js
/** @param fooBarService The Bar service for the Foo application. */
```

- Because of this rule, `@param` and `@return` lines are only required when they add information, and _may_ otherwise be omitted.

```js
/**
 * POSTs the request to start coffee brewing.
 * @param amountLitres The amount to brew. Must fit the pot size!
 */
brew(amountLitres: number, logger: Logger) {
  // ...
}
```

### Comments when calling a function

“Parameter name” comments should be used whenever the method name and parameter value do not sufficiently convey the meaning of the parameter.

Before adding these comments, consider refactoring the method to instead accept an interface and destructure it to greatly improve call-site readability.

Parameter name comments go before the parameter value, and include the parameter name and a `=` suffix:

```js
someFunction(obviousParam, /* shouldRender= */ true, /* name= */ 'hello');
```

Existing code may use a legacy parameter name comment style, which places these comments ~after~ the parameter value and omits the `=`. Continuing to use this style within the file for consistency is acceptable.

```js
someFunction(obviousParam, true /* shouldRender */, 'hello' /* name */);
```
