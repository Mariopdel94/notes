#source #clean-code

Notes to self:
* A very big part of this CoP could be code review of existing code from members
* Shoot for getting one person from each team to be a part of this CoP
* How is clean code measured by each own standards and practices?
* Get good practices in other languages

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

^639e63

## Bad code
Have you ever been significantly **impeded by bad code**? If you are a programmer of any experience, then **of course you have**. 

We try to find our way through bad code, hoping to get some hint, some idea, some clue of what the \*\*\* is going on... But all we see is more and more **senseless** code.

So, of course you've been impeded by bad code. But then... **why did you write it?**

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

![[Functions - First Rule]]
### Blocks and Indenting
Given the [[Functions - First Rule|first functions rule]] this means that `if`, `else` and `switch` statements **should be one line long**. This does not only ensures the function is kept small, but it also adds documentary value by having a nice descriptive name. This also implies that functions should not hold nested structures it should not be greater than one or two levels.

# Angular specific
 
## Convert **imperative** code to **reactive** code.

Some of the key ideas you can use to convert imperative code to a more reactive pattern are two *Karen* inspired rules you can follow to get more into that declarative/reactive pattern mindset:

1. [[Angular - Make sure things never change]]
2. [[Angular - "I want to speak to your manager"]]
### Refactor code!
Okay, we now know that imperative is bad and we should avoid it and reactive is good. But how do we apply these two rules?

1. [[Angular - Make sure things never change (Refactor)]]
2. [[Angular - "I want to speak to your manager" (Refactor)]]
