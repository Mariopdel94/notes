#clean-code #good-practice #example

What does the following code do?

```typescript
public getThem(): number[][] {
	const list1: number[][] = [];
    for (const x of theList) {
        if (x[0] === 4) {
            list1.push(x);
        }
    }
    return list1;
}
```

Why is so hard to tell what this code is doing? Spacing and indentation are reasonable. There is no fancy stuff going around. 

The problem is not the simplicity or how hard the code is. The problem is the *implicity* of the code. The code implicitly expects us to know:
* What is the type of `theList` and the elements inside? 
* What does it mean the zeroth subscript `x[0]` of an item of `theList`?
* What does the `4` mean?
* How is the returned `list` used?

Let's say the code is for a minesweeper game, we find out `theList` actually corresponds to the `gameBoard`. So let's rename it. 

We also find out that each cell on the board is represented by an array and the zeroth subscript holds a status value, and the status value of 4 means "flagged". Just by adding these names the code improves massively:

```typescript
public getFlaggedCells(): number[][] {
    const flaggedCells: number[][] = [];
    for (const cell of gameBoard) {
        if (cell[STATUS_VALUE] === FLAGGED) {
            flaggedCells.push(cell);
        }
    }
    return flaggedCells;
}
```

We can even go further and create a `Cell` class and implement `isFlagged` method to hide the "magic numbers".

```typescript
public getFlaggedCells(): Cell[] {
    const flaggedCells: Cell[] = [];
    for (const cell of gameBoard) {
        if (cell.isFlagged()) {
            flaggedCells.push(cell);
        }
    }
    return flaggedCells;
}
```

Notice how the code now is incredible simple to read and immediately understand what it does. [[Code Quality - Code Craftsmanship#^639e63|Clean code reads like well-written prose]]

# Real life example

In a MR I found the following code where the equality of a constant is assigned to a variable, but the variable name does not provide any information as to why that equality exists.

```typescript
const hasEarnedSixPoints = points === 6;
```

This was easily changed to:

```typescript
const hasEarnedMaxPoints = points === MAX_EARNABLE_POINTS;
```

------------------------------------------- 

In an existing file I saw the following code:

```typescript
export const isPartial = createSelector(
  paymentResultSlice.selectors.selectLatestCollection,
  (latestCollection) => {
    return latestCollection.filter(
      (item) =>
        item.paymentMedia?.status === 'APPROVED' &&
        (item.paymentMedia?.statusCode === 12 ||
          item.paymentMedia?.statusCode === 14 ||
          item.paymentMedia?.statusCode === 48)
    )[0];
  }
);

export const historyContainsPartial = createSelector(
  paymentResultSlice.selectors.selectLatestCollection,
  (collection) => {
    return (
      collection.filter(
        (result) =>
          result.paymentMedia?.statusCode === 12 ||
          result.paymentMedia?.statusCode === 14 ||
          result.paymentMedia?.statusCode === 48
      ).length > 0
    );
  }
);
```

The name of the variables is okay, the problem is first of all the code is duplicated. And we have no idea what 12, 14, not 48 mean. There are some cases where our developer experience might give us some implicit context for example: `if (apiResponseStatus === 200)` we know that 200 means "successful", but in this case 12, 14, nor 48 belong to a standardized practice like the HTTP responses. 

