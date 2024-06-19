#source #clean-code

Notes:
* A very big part of this CoP could be code review of existing code from members
* Shoot for getting one person from each team to be a part of this CoP

# Clean Code

[[Small things matter]]

> The smallest bit of sloppy construction, of the door that does not close tightly or the slightly crooked tile on the floor, or even the messy desk, completely dispels the charm of the larger whole. That is what clean code is about.

[[Maintenance over producing]]
When we spend more of our time maintaining the codebase, we should focus more on how to facilitate it than focusing on "creating" new code. 

>We are going to look at code from every different direction. We’ll look down at it from the top, up at it from the bottom, and through it from the inside out. By the time we are done, we’re going to know a lot about code. What’s more, we’ll be able to tell the **difference between good code and bad code**. We’ll know how to write good code. And **we’ll know how to transform bad code into good code.**

[[The Boy Scout Rule]]

## What is "clean code"?

According to Grady Booch, author of Object Oriented Analysis and Design with Applications. 

>Clean code is simple and direct. Clean code reads like well-written prose. Clean code never obscures the designer’s intent but rather is full of crisp abstractions and straightforward lines of control.

^6fb3ee
## Bad code
Have you ever been significantly **impeded by bad code**? If you are a programmer of any experience, then **of course you have**. 

We try to find our way through bad code, hoping to get some hint, some idea, some clue of what the *** is going on... But all we see is more and more **senseless** code.

So, of course you've been impeded by bad code. But then... **why did you write it?**

## Use Intention-Revealing Names
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

### Example:
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

Notice how the code now is incredible simple to read and immediately understand what it does. [[Code Quality - Code Craftsmanship - CoP (Community of Practice)#^6fb3ee|Clean code reads like well-written prose]] 
# Angular specific
 
## Convert **imperative** code to **reactive** code.

Some of the key ideas you can use to convert imperative code to a more reactive pattern are two *Karen* inspired rules you can follow to get more into that declarative/reactive pattern mindset:

1. [[Angular - Make sure things never change]]
2. [[Angular - "I want to speak to your manager"]]
### Refactor code!
Okay, we now know that imperative is bad and we should avoid and reactive is good. But how do we apply these two rules?

1. [[Angular - Make sure things never change (Refactor)]]
2. [[Angular - "I want to speak to your manager" (Refactor)]]
