#typescript #file-structure
## Imports

| Import type | Example | Use for |
| --------------------------- | -------------------------------- | --------------------------------------------------------------------------------- |
| module[module_import] | `import \* as foo from '...';` | TypeScript imports |
| named[destructuring_import] | `import {SomeThing} from '...';` | TypeScript imports |
| default | `import SomeThing from '...';` | Only for other external code that requires them |
| side-effect | `import '...';` | Only to import libraries for their side-effects on load (such as custom elements) |

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

```ts
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
