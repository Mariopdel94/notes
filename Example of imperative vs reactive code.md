Class: 
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

Template: 
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

Here we have an example of code that isn't necessarily bad. In fact is what most AI/LLMs would suggest, and what you will find in most StackOverflow answers... BUT this is usually how our brains start to work when we are coding, we tend to think on a set of steps to take to get to our goal, and we end up writing **imperative code**

The main problems here are:
* There is a manual subscription, in which we imperatively set not only one but two variables `addressSuggestions` and `showSuggestions` from the result of the request/rxjs pipe 
* This ends up in the fact that whenever we need to make an update we need to always remember to update all manual variables (see `onSuggestionClick` and `onSearchInput`; look at all those manual `set`)
* The variables declared at the beginning of the file even though they have an initial value, we have no idea how they behave, what they depend on, 