#good-practice 

Use named functions inside the effects instead of an empty arrow callback.

## Why?
All variables (and functions!) should have a clear, informative name
functions should be short and only do one thing

## Why not use a comment instead?
**[[The best comment is no comment at all]]**. Code should be self-explanatory and variable and functions declaration should display their intention through concise logic and good naming

## Pros:
- We will have more clarity in the purpose of the effect
- If there's an error in the effect, the function's name will show up in the stack trace
- Psychologically will make the developer to make the effect do only one thing splitting different tasks into multiple effects if necessary.

## Cons:
- Having to use a slightly longer syntax by using the keyword function 

## Example:

```ts
// Bad example:
useEffect(() => {
	if (sevenBoss.status === SyncState.FAILURE) {
	  if (!ff_isSauceManagingStoreAssociate) navigate('/lane-closed');
	}
}, [sevenBoss]);
```

```ts
// Good example!
useEffect(
  function checkIfLaneIsClosed() {
    if (sevenBoss.status === SyncState.FAILURE) {
      if (!ff_isSauceManagingStoreAssociate) navigate('/lane-closed');
    }
  },
  [sevenBoss]
);
```