#typescript #file-structure 

Use named exports in all code:

```ts
    // Use named exports:
    export class Foo { ... }
```

Do not use default exports. This ensures that all imports follow a uniform pattern.

```ts
    // Do not use default exports:
    export default class Foo { ... } // BAD!
```

Why?

Default exports provide no canonical name, which makes central maintenance difficult with relatively little benefit to code owners, including potentially decreased readability:

```ts
import Foo from './bar'; // Legal.
import Bar from './bar'; // Also legal.
```

Named exports have the benefit of erroring when import statements try to import something that hasn't been declared. In `foo.ts`:

```ts
const foo = 'blah';
export default foo;
```

And in `bar.ts`:

```ts
import { fizz } from './foo';
```

Results in `error TS2614: Module '"./foo"' has no exported member 'fizz'.` While `bar.ts`:

```ts
import fizz from './foo';
```

Results in `fizz === foo`, which is probably unexpected and difficult to debug.

Additionally, default exports encourage people to put everything into one big object to namespace it all together:

```ts
    export default class Foo {
      static SOME_CONSTANT = ...
      static someHelpfulFunction() { ... }
      ...
    }
```

With the above pattern, we have file scope, which can be used as a namespace. We also have a perhaps needless second scope (the class `Foo`) that can be ambiguously used as both a type and a value in other files.

Instead, prefer use of file scope for namespacing, as well as named exports:

```ts
    export const SOME_CONSTANT = ...
    export function someHelpfulFunction()
    export class Foo {
      // only class stuff here
    }
```