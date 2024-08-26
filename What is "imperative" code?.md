Let's start with some basic variables declarations:

```typescript
comments = ['hi', 'hello', 'how are you?']
lastComment = comments[comments.length - 1];
```

(Btw this code is fully reactive... as long as these values never change)

We know from looking at both variables declaration what they do, how they do it, and their dependencies.

But what would happens if we do this?

```typescript
comments = ['hi', 'hello', 'how are you?']
lastComment = comments[comments.length - 1];

someMethod(){
  this.comments.push('uh oh');
}
```

**Now everything has fallen apart.**

![a cartoon dog is sitting at a table with a cup of coffee in front of a fire with the words this is fine .](https://media1.tenor.com/m/MYZgsN2TDJAAAAAC/this-is.gif)

 Can I look at `comments` and know what it **is** just by looking at its declaration? No. Not anymore. I know what it is _initially_, but to understand its full behavior over time I need to find anywhere else in the code where it is being **imperatively** changed. Our `someMethod` **imperatively** changes what `comments` is.
 
There is also a problem with `lastComment` now. It’s value is only ever set to what `comments` was initially — when `comments` is updated, `lastComment` is **not** updated. We need to do that manually… or **imperatively**:

```typescript
comments = ['hi', 'hello', 'how are you?']
lastComment = comments[comments.length - 1];

someMethod(){
  this.comments.push('uh oh');
  this.lastComment = this.comments[this.comments.length - 1];
}
```

We are already getting a preview of the benefits of declarative code — **the imperative code above is _super_ prone to bugs**. If we forget to manually update `lastComment` it will not have the correct value.

# Reactive alternative

```typescript
comments = signal(['hi', 'hello', 'how are you?'])
lastComment = computed(() => this.comments()[this.comments().length - 1])

someMethod(){
  this.comments.update((comments) => ([...comments, 'a bit better']))
}
```

This is still not completely declarative — we are still imperatively updating `comments`. How can we tell this is **imperative**? Because we don’t really know what `comments` is unless we also find and understand what `someMethod` is doing. We can’t just look at the **declaration** of `comments`.

But, our `lastComment` is declarative and now it will be automatically updated when `comments` updates without us needing to do it manually.

An Angular application can not really be “fully” declarative. Often we will have some small imperative operations happening, and then have everything after that point be reactive/declarative.

# Imperative promises & observables

```typescript
// (text is reassigned after declaration)
let text = 'Any text in here';
text = await somePromise();

// myVal is reassigned after declaration
let myVal;
myObservable$.subscribe(val => {
	myVal = val;
})

...

someOtherFunction() {
	this.text = 'Now theres a new value!';
}
```

With imperative code the definition of a "thing" and how its value can change over time is spread out throughout the codebase. 