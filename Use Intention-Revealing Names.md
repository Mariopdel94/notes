#clean-code #good-practice 

It is easy to say that names should reveal intent. Choosing good names usually takes time, and thinking, but reading the code afterwards saves more time than it.

The name of a variable, method, function, class, should **always** reveal its intention and answer all big questions... Why it exists? What it does? How it is used? If a comment is needed to answer any of these, then the name does not reveal its intent.

This is an over exaggeration of a bad naming example. The variable is not even a word, is not initialized (which will be covered on a separate topic), and does not disclose any information at all.

```typescript
const d; // elapsed time in days
```

A more real (still bad) example would be the following. Now we have a word, and is the "important" bit of information needed. But the name is too generic, and can be confused with so many different usages, it does not disclose it holds the elapsed time, it could be "time needed for...", "days since X...", etc.

```typescript
const days; // elapsed time
```

We should choose a name that specifies what is being measured and the unit of that measurement:

```typescript
const elapsedTimeInDays;
const daysSinceCreation;
const daysSinceModification;
const fileAgeInDays;
```

Do not be afraid of having a longer variable name (try to keep it as short as possible). Reading the code afterwards will make it much more faster.

## Example:
[[Intention-Revealing Names (Example)]]
