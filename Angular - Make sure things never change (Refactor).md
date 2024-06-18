#clean-code #angular 

Having the following code:

```typescript
private myData: MyData[] = [];
```

In reality, this code **is already declarative**... as long as `myData` is just actually an empty array that never changes. Because "things *should* never change".

```typescript
private readonly myData: MyData[] = [];
```

The tricky part is that most probably we do want these values to change over time. So how do we do this? We need to find a reactive mechanism to allow the values to change **within its declaration and without reassigning the variable itself**. 

```typescript
private myData: ReactiveThing<MyData[]> = someReactiveMechanism(() => {
	// What does this react to?
	// How does it calculate its value?
});
```

This **reactive mechanism** could be: RxJS, Angular signals, a combination of these two, or something else entirely. 

In this particular case, it is simple because `myData` are just the values returned from the `getData` response.

Imperative example:
```typescript
export class MyComponent implements OnInit {
	private myService = inject(MyService);
	private id = 1;
	private myData: MyData[] = [];
	  
	ngOnInit() {
		this.myService
			.getData(this.id)
			.pipe(takeUntilDestroyed())
			.subcribe((data) => {
				// Breaks "declarative-ness"
				this.myData = data;
			});
	}
}
```

Reactive:
```typescript
private myData$: Observable<MyData[]> = this.myService.getData(this.id);
```

Now `myData$` **is** the observable stream of data. This is a complete definition of "myData". This is declarative, **and the value can change** over time (it depends on `id`). Everything we need to know, what the thing is, and how it behaves is contained within its declaration.

And now we can also declaratively define other things that **depend** on that reactive variable.

```typescript
private myData$: Observable<MyData[]> = this.myService.getData(this.id);

private filteredData$ = this.myData$.pipe(
	map((data) => this.filterData(data)),
);
```

Now there is **no need to manually detect** when `myData` changes. It is done automatically... reactively. It will be recalculated when whatever depends on changes (in this case `myData`), and how it is calculated it contained entirely within its declaration. 

### Signals

You can even wrap the RxJS code from above to Angular Signals.

```typescript
public filter = signal<FilterType>('none');
private myData = toSignal(this.myService.getData(this.id));
private filteredData = computed(() => this.filterData(this.myData(), this.filter()))

private filterData(data: MyData[], filter: FilterType) {
	// Filter data somehow
	return result;
}
```