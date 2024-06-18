#clean-code #angular 
# How to get into the "declarative/reactive" mindset?

## "I want to speak to your manager"

Imagine the following code flow:

1. We have an `event binding` in the `template`, that calls a `method`.
2. That `method` calls a `service` to get some data.
3. The `component` does some stuff with it and **imperatively** updates the state.

> See drawing pink arrow flow from the template:
![[Angular - Make sure things never change 2024-06-18 12.25.07.excalidraw|1920]]

This is being imperatively reassigned, and in order to understand what this entity in our application is, we need to trace through all the code pathways that **imperatively** set it. After examining those pathways we know that an event binding in the template updates this value... or at least we know that **until we forget about it**.

The definition of the **thing** is dispersed throughout the application, there could be many methods that will cause the value to update in unknown ways... This is the nature of imperative style code.

## Declarative alternative

>See: [[Declarative variable definitions]]
![[Angular - "I want to speak to your manager" 2024-06-18 13.03.18.excalidraw|1920]]

### "RxJS is complicating things"

Something Angular always gets hated on, is the dependency on RxJS and how it is "hard" and "overcomplicates" things. In reality RxJS helps with the reactive patterns and actually keeps the code simpler (once you get the grasp of [[RxJS pipes]])

## How do we refactor this?

[[Angular - "I want to speak to your manager" (Refactor)]]

