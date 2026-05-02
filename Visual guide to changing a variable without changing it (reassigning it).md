In declarative code we have established that we don't reassign variables after their initial declaration. This seems to be a conceptual blocker for some people and it makes sense because data in our application changes over time.  How can we represent those changes if we can never reassign a variable. **It is like asking someone to get dressed without changing their clothes.**

 Imagine the following example: we have a data variable for a page which will hold the items available in a shop. Initially we can declare it like this:

```typescript
data = ['vegetables', 'fruits', 'candies'];
```

Remember, this is already declarative as long as this variable never gets reassigned.  But what if this `data` is subject to changes? What if we need to load this `data` from a DB?

**The declaration of our `data` needs to describe/declare this potential change upfront**  and as part of this declaration it will describe its dependency on the request to the database. 

Thus a reactive graph is created like this:

![[Visual guide to "changing a variable without changing it (reassigning it)" 2024-08-07 10.48.47.excalidraw|1920xauto]]

We have our request to the database as our **initiator of change**, this will start a chain reaction of other changes. Our `data` declaration depends on that request. When the request declaration emits some value then our `data` will react to it, and that reaction is declared upfront in the declaration of our `data`.

**Our `data` value can now change at runtime without ever being reassigned.**

We need to be able to build these reactive graph models in our heads or on paper, in order to build reactive and declarative code. The RxJS implementation comes second to this, and RxJS is not really needed, this can be achieved with any reactive mechanism, like signals or one of your own.

This example is fairly simple, but what if we want to add **pagination** to our UI?

Now we need to consider how we would modify this reactive graph to introduce a new element: pagination.

We create a new variable with the current page number:

```typescript
page = new BehaviorSubject(1);
```

But where does this declaration would go in our reactive graph? What is the most fundamental declaration? **Which of our 3 variables is the initiator of change that everything else should react to?** 

It is actually the page number, this now sits at the top because the request depends on the page number. Any time the page number changes the request will be re-triggered.

![[Visual guide to "changing a variable without changing it (reassigning it)" 2024-08-07 11.12.35.excalidraw|1920xauto]]

Now let's add a `filter`:

Let's ask the question of where this "node" lives in our graph, and how everything else would react to it (if anything) and what should this filter react to (if anything). It actually depends on what we need to accomplish. If we want to filter the data locally then we would have something like this:

 ![[Visual guide to "changing a variable without changing it (reassigning it)" 2024-08-26 13.28.03.excalidraw|1920xauto]]
This setup **does not** trigger a new HTTP request from the server when the `filter` value updates. Because the `request` declaration has no dependency on the filter declaration.

But if we want to filter the data pulled from the DB then the graph would look different (and thus the code).

![[Visual guide to "changing a variable without changing it (reassigning it)" 2024-08-26 13.33.45.excalidraw|1920xauto]]

Now anytime the `filter` or the `page` value changes it will trigger a new `request` and the `data` will be updated. 

