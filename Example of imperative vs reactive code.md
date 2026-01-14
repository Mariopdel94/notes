## Imperative code / Manual subscriptions

Take a look at this code, it is a simple searchbar with an autocomplete; do you see anything wrong with it? From a first look, and to the untrained eye it might look like everything is fine, in fact most sources will suggest a similar approach.

```typescript
private readonly destroyRef = inject(DestroyRef)
private readonly searchQuery$ = new Subject<string>()

/** Search query for filtering addresses */
readonly searchQuery = signal('')

/** Whether to show address suggestions */
readonly showSuggestions = signal(false)

/** Address suggestions from service */
  readonly addressSuggestions = signal<Address[]>([])

  constructor() {
    this.searchQuery$
      .pipe(
        debounceTime(300),
        distinctUntilChanged(),
        switchMap((query) => 
	        this.addressService.searchAddresses(query)),
	        takeUntilDestroyed(this.destroyRef)
      )
      .subscribe((suggestions) => {
        this.addressSuggestions.set(suggestions)
        this.showSuggestions.set(
          suggestions.length > 0 || this.searchQuery().length > 0
        )
      })
  }
  
  onSearchInput(event: Event): void {
    const input = event.target as HTMLInputElement
    this.searchQuery.set(input.value)
    this.showSuggestions.set(input.value.length > 0)
    this.searchQuery$.next(input.value)
  }

  onSuggestionClick(address: Address): void {
    this.searchQuery.set('')
    this.showSuggestions.set(false)
    this.addressSuggestions.set([])
    this.addressSelected.emit(address)
  }
```

```html
<input
  matInput
  type="search"
  name="deliveryAddress"
  placeholder="Delivery Address"
  [value]="searchQuery()"
  (input)="onSearchInput($event)"
  autocomplete="off"
>
```

The code that isn't necessarily bad. In fact is what most AI/LLMs would suggest, and what you will find in most StackOverflow answers... BUT this is usually how our brains start to work when we are coding, we tend to think on a set of steps to take to get to our goal, and we end up writing **imperative code**

### Main Problems

* There is a manual subscription, in which we imperatively set not only one but two variables `addressSuggestions` and `showSuggestions` from the result of the request/rxjs pipe 
* This ends up in the fact that whenever we need to make an update we need to always remember to update all manual variables (see `onSuggestionClick` and `onSearchInput`; look at all those manual `set`)
* The variables declared at the beginning of the file even though they have an initial value, we have no idea how they behave, what they depend on, etc. (see `searchQuery`, `showSuggestions`, and `addressSuggestions`)
* We have "repeated" variables (even though they are needed) for `searchQuery`. One for the observable stream and another for the input value.
* We need to remember to `takeUntilDestroyed` the observable otherwise we could have memory leaks.

Oof, the functionality is fairly simple (just a searchbar) and we have all these underlying (possible) issues.

## How can we avoid this? How can we improve this?

First of all we can notice that inside the manual subscription the variables we manually set inside are **dependant** from the stream of data, in other words these variables **react** to the stream of data.

Taking the "Karen" approach - We don't need to talk to these variables directly, instead we want to speak to their manager
[[Angular - "I want to speak to your manager"]]

But now with Angular 21+ we have (experimentally) **signal forms**, so we now have a better way of subscribing to the value changes from an input.

To do that we need to create a form:
```typescript
formModel = signal({
    searchQuery: '',
  })
form = form(this.formModel, (schema) => {
	debounce(schema.searchQuery, 300)
})
```

In here we specify the `debounce` on the schema directly on the form declaration. And we can remove `distinctUntilChanged` because a signal doesn't emit unless there's a value change.

With that we know can change our declaration of the dependent variables:

```typescript
readonly addressSuggestions = rxResource({
    defaultValue: [],
    params: () => ({ searchQuery: this.form.searchQuery().value() }),
    stream: ({ params }) =>
      this.addressService.searchAddresses(params.searchQuery),
  })
  
readonly showSuggestions = computed(() => {
    const hasSuggestions = this.addressSuggestions.value().length > 0
    const hasTypedQuery = this.form.searchQuery().value().length > 0
    return hasSuggestions || hasTypedQuery
  })
```

We can see we are using the (experimental) `rxResource` which takes the `searchQuery` value as a parameter and every time it updates (with the set debounce we specified on the form) we recalculate through the specified stream with the `addressService`. Same thing for the `showSuggestions` variable, it updates automatically, in other words it **reacts** to the variables it depends on.

These variables now have their initial value, what they depend on, how they behave, everything we need to know from its **declaration**

What happened to `onSearchInput`? It got deleted, it is now no longer needed. And `onSuggestionClick` now has way fewer imperative steps. (It is impossible to remove all imperative actions)

```typescript
  onSuggestionClick(address: Address): void {
    this.form.searchQuery().value.set('')
    this.addressSelected.emit(address)
  }
```

But because everything else **reacts** from the form value, resetting the form updates everything else automatically.