#source #clean-code

Notes to self:
* A very big part of this CoP could be code review of existing code from members
* Shoot for getting one person from each team to be a part of this CoP
* How is clean code measured by each own standards and practices?
* Get good practices in other languages/frameworks

# Clean Code

## Bad code
Have you ever been significantly **impeded by bad code**? If you are a programmer of any experience, then **of course you have**. 

We try to find our way through bad code, hoping to get some hint, some idea, some clue of what the \*\*\* is going on... But all we see is more and more **senseless** code.

So, of course you've been impeded by bad code. But then... **why did you write it?**

We’ve all said we’d go back and **clean it up later**. Of course, in those days we didn’t know LeBlanc’s law
> Later equals never.

## What is "clean code"?

According to Grady Booch, author of Object Oriented Analysis and Design with Applications. 

>Clean code is simple and direct. Clean code reads like well-written prose. Clean code never obscures the designer’s intent but rather is full of crisp abstractions and straightforward lines of control.

^639e63

**Writing clean code is a lot like painting a picture**. Most of us know when a picture is painted well or badly. But being able to recognize good art from bad does not mean that we know how to paint. So too being able to recognize clean code from dirty code does not mean that we know how to write clean code!

[[Small things matter]]

> The smallest bit of sloppy construction, of the door that does not close tightly or the slightly crooked tile on the floor, or even the messy desk, completely dispels the charm of the larger whole. That is what clean code is about.

Imagine the smallest crack in your car windshield, yes it is small, and perhaps out of the way on your day-to-day vision, but as you drive more and more that crack slowly becomes a much bigger problem, and you end up needing to replace the whole windshield, yet you could've repaired it when the crack was small enough.

[[Maintenance over producing]] 🔨
When we spend more of our time maintaining the codebase, we should focus more on how to facilitate it than focusing on "creating" new code. 

Imagine working on a project where code *simply got better* as time passed, instead of the usual code that slowly and steady rots into a messy bad code. **Isn’t continuous improvement an intrinsic part of professionalism?**  Thus: [[The Boy Scout Rule]] ☝️☝️☝️

## Meaningful names

There are many naming rules and conventions but that is for you to decide which rules to implement in your codebase and how to implement them. Regardless most common ones are:

* Use Intention-Revealing Names. [[Use Intention-Revealing Names| It is easy to say that names should reveal intent. Choosing good names usually takes time, and thinking, but reading the code afterwards saves more time than it. The name of a variable, method, function, class, should always reveal its intention ]]
* Avoid names that vary in small ways. 
* [[Make meaningful distinctions|Make meaningful distinctions - Distinguish names in such a way that the reader knows what the differences offer.]]
* Use pronounceable names. Avoid names like `genymdhms` which are unpronounceable and doesn't reveal intent from the get-go. Instead `generationTimestamp` allows for an intelligent English conversation with your peers.
* [[Class names should be nouns]]. Never verbs
* [[Method names should be verbs]]. 
* [[Naming Boolean variables|Booleans must start with an `is` prefix, or a modal verb]]
* [[Make meaningful context]]. If you read  the variable `state` what do you think it means?

## Functions

![[Functions should do one thing]]

![[Functions - First Rule]]
### Blocks and Indenting
Given the [[Functions - First Rule|main rule of functions]] this means that `if`, `else` and `switch` statements **should be one line long**. This does not only ensures the function is kept small, but it also adds documentary value by having a nice descriptive name. This also implies that functions should not hold nested structures it should not be greater than one or two levels.

This also applies to nested ternaries, it should not be greater than one level. Take a look at this:

```typescript
 const slideUpPanelStartPosition =
    isUnregisteredUser && !!clubProgressData
      ? applyPixelRatio(-40)
      : isUnregisteredUser
      ? applyPixelRatio(-80)
      : applyPixelRatio(300);
```

By reading this, do you know what is the initial value of this? Even if we change it to an `if/else` chain:

```typescript
let slideUpPanelStartPosition;
if (isUnregisteredUser && !!clubProgressData) {
    slideUpPanelStartPosition = applyPixelRatio(-40);
  } else if (isUnregisteredUser) {
    slideUpPanelStartPosition = applyPixelRatio(-80);
  } else {
    slideUpPanelStartPosition = applyPixelRatio(300);
  }
```

Usually a good way to handle an `if/else` chain is to look at it the other way around and reverse it. For example:

```typescript
  let slideUpPanelStartPosition = getPanelStartPosition();
  function getPanelStartPosition() {
    const default = applyPixelRatio(300);
    if (isUnregisteredUser && !!clubProgressData) return applyPixelRatio(-40);
    if (isUnregisteredUser) return applyPixelRatio(-80);
    return default;
  }

```

[[Functions - Cleanliness refactor example]]
[[Functions - No side effects]]
It is mentioned before, but variables (and functions) names should have [[Make meaningful context|meaningful context]]. The following are real life example of our own project where naming is confusing and the developer needs to dive deeper into the rabbit hole just to begin understanding what is going on. [[Functions - Meaningful context (example)]]


## Comments

I've already declared that [[The best comment is no comment at all]] and usually writing a comment is just a reflection of bad code. And on most cases it ends up in old and outdated comments that are no longer true. 

The **code changes, and it evolves**, constantly while comments are left unattended. We can make the argument that developers should be disciplined enough to constantly update the comments, but I would make the counterargument that that energy should be better used in writing cleaner code. 

It is far better to have clean code with no comments at all, than having a cluttered complex file with lots of code. 

Example:

```typescript
// Check to see if the employee is eligible for benefits
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65)) {}
```

When the code can be simplified to:

```typescript
if (employee.isEligibleForBenefits()) {}
```

Remember that clean code should be a [[Code Quality - Code Craftsmanship#^639e63|well-written prose]]
# Angular specific

[[Visual guide to "changing a variable without changing it (reassigning it)"]]

## Convert **imperative** code to **reactive** code.

Some of the key ideas you can use to convert imperative code to a more reactive pattern are two *Karen* inspired rules you can follow to get more into that declarative/reactive pattern mindset:

1. [[Angular - Make sure things never change]]
2. [[Angular - "I want to speak to your manager"]]
### Refactor code!
Okay, we now know that imperative is bad and we should avoid it and reactive is good. But how do we apply these two rules?

1. [[Angular - Make sure things never change (Refactor)]]
2. [[Angular - "I want to speak to your manager" (Refactor)]]

# React specific
[[Personal take on React destructuring props|Controversial personal take on React destructuring all props]]