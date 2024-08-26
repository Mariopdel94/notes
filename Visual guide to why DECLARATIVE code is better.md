Imagine we have a variable that holds the value of the current **temperature** and we get this value from an asynchronous source like a DB. 

With the **declarative** approach, the code for defining **how that value is retrieved is contained within the declaration itself**.

For the **imperative** example, there is a separate process that handles getting that value and setting it. Thus there is **no clear connection between the initial declaration and where the its value is set**. To find this connection you would need to find the process that handles this fetching and assigning the value to your variable, all while looking around for other places that might reassign this variable to something else. Only when you see this separate process `initTemp` is when it becomes clear the connection between the initial declaration and the its source (the DB / HTTP request).

![[Code Quality - Code Craftsmanship 2024-08-26 10.57.18.excalidraw|1920xauto]]

Now lets move this example a step further. Let's say that the value for `temp` can be updated. This can be done automatically or manually by clicking a UI button, it really doesn't matter how, but it can be refreshed/cleared/updated by a separate source. 

With the declarative approach it again, follows a chain of commands that you can know in advance when looking at the declaration of your `temp` variable. Its declaration displays the source dependencies of it and how it will behave.

On the other hand, with the imperative approach yet another method is introduced with no clear connection to the variable, and now you have to keep track of all these methods in your head if you decide to use or update your `temp` variable. 

![[Code Quality - Code Craftsmanship 2024-08-26 11.14.15.excalidraw|1920xauto]]

Let's add a third step into this example. Let's say we now have a separate variable that updates depending on the temperature variable. Is it hot outside? 

With the declarative approach is very straight forward, we just use the `temp` variable as the source of this new variable `isHot`. There is a clear connection between these variable variables that is visible starting in the declaration of it. And the `isHot` variable also contains how it behaves and all the necessary logic to determine if it is `true` or `false`. 

With this reactive approach anytime the `temp` variable updates `isHot` will also be **automatically updated**.

But in the imperative example there is no clear connection between the two variables (`temp` and `isHot`) and the update of these variables needs to be done in separate processes.

![[Code Quality - Code Craftsmanship 2024-08-26 11.28.27.excalidraw|1920xauto]]

## Let's scale this up!
 ![](https://i.makeagif.com/media/1-09-2022/GNwzsf.gif)

![[Visual guide to why DECLARATIVE code is better 2024-08-26 11.49.26.excalidraw|1920xauto]]

## Ok, reactive code is better we get it. Does that mean we should make all code reactive?

I am not saying we should do 100% declarative/reactive code, there must be some imperative code for some handlers, but usually these should ideally be hidden behind declarative wrappers or should be limited to small tasks (usually to trigger a declarative source in response of a user action in the UI). 