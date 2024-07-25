In react is common practice to destructure the props objects in each component like this:

```typescript
export const AlertToast = ({ type, text, subtext, icon, iconPosition, dismissable, styles, }: ToastAlertProps) => {}
```

But this leads to two main issues:

1. Using `icon` directly could lead to the developer mistakenly giving an incorrect usage to the variable. For example, **reassigning the value of it**, neither React nor the IDE raises any warnings for this, but I would advise this to be counter intuitive since the **Props are meant to be used only on the component creation as readonly variables**.
2. We add a "human" step to our code, if we update the definition of `ToastAlertProps` we now need to also add/remove it in each component/object that is destructured it **manually**.

As previously mentioned, we can also go by adding a prefix or a suffix to make the variable more [[Use Intention-Revealing Names|intent-revealing]] for example: `toastIcon` or `iconProp`.  The problem with this is now we are cluttering the variable names and the IDE will not know the context of the variable. **Make meaningful context**.

Having a parent object (`Props`) provides more context that the variable is part of a bigger picture, and not only the reader would know this but also the IDE. Destructuring the props directly in the callback makes the IDE treat the variable as `parameter` which in turn is interpreted as `var` and this means we can overwrite it without any sorts of warning, and as previously mentioned this means the variable now is treated in a different scope that was intended.

```typescript
export const Toast = ({ icon }: ToastAlertProps) => {
  icon = 'I can assign whatever I want in here';
}
```

Notice how the reassign doesn't have `const` nor `let`. Not to mention that also the IDE *thinks* the `icon` prop is used (VSCode greys out the unused variables) when in reality it isn't.

Now the usage of this component might be weird, if in our `CartComponent` we include this newly `ToastComponent` like so:

```jsx
<Toast icon={'check'}/>
```

But the `check` icon is nowhere to be found, then the app is not behaving how it is expected. It breaks its "declarative-ness"

## Middle ground for React props

```typescript
export const Example = (props: ToastAlertProps) => {
  const { icon } = props;
  icon = 'I no longer can update this becasue is a `const`';
}
```

![[Pasted image 20240725102110.png]]

If we destructure the props object **inside** the component, as `const` then all those variables will be treated as `readonly` (as they should). We would still "lose" the general context of the upper object, but this way we can keep the "react pattern" used so far. 

## Does this mean destructuring is bad?

No! Of course not. As always in programming, **it depends**. 

Let's look at a good examples of when destructuring is useful:

```typescript
let [firstName, surname] = "John Smith".split(' ');
```

Destructuring arrays and assigning useful descriptive names the variables is very good, now the code is more readable, and provides more descriptive context.

What about an object?

Destructuring an object is useful when we plan to use only some of the properties but not all. Another place it might be useful is in small scope functions. Functions should be kept small and simple, so we can destructure only the variables we plan on using inside that scope.

Imagine this function inside the `ToastComponent`:

```typescript
const getCapitalizedDangerTitle = (props: ToastAlertProps) => {
const { type, title } = props;
if (type === DANGER) return title.toUppercase();
}
```

This might be a simple example, but it portrays the basic concept. We are not using the entirety of the props object, we are keeping them as `readonly` and the scope is kept small. 

