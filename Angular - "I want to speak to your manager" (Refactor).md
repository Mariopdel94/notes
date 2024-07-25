#clean-code #angular

So how do we refactor a situation where we have an event binding on the template that triggers a method that then updates some value?

Having a mindset of "I want to speak to your manager! 😡😡😡"

Imperative case:
```typescript
export class MyComponent implements OnInit {
	private myService = inject(MyService);
	private id = 1;
	private myData: MyData[] = [];
	protected filteredData: MyDatap[] = this.myData;
	  
	ngOnInit() {
		this.myService
			.getData(this.id)
			.pipe(takeUntilDestroyed())
			.subcribe((data) => {
				this.myData = data;
				// Manually update derived state
				this.filteredData = this.filterData(data);
			});
	}
	
	private filterData(data: MyData[]) {
		// ... Filter data somehow
	}
}
```

A good example would be to have a `button` on the template that allows the user to clear the current filters. 

The imperative code flow would be:
1. Trigger some method through that will handle resetting the filter by reassigning the `filteredData`.

```typescript
export class MyComponent implements OnInit {
	private myService = inject(MyService);
	private id = 1;
	private myData: MyData[] = [];
	protected filteredData: MyDatap[] = this.myData;
	  
	ngOnInit() {
		this.myService
			.getData(this.id)
			.pipe(takeUntilDestroyed())
			.subcribe((data) => {
				this.myData = data;
				// Manually update derived state
				this.filteredData = this.filterData(data);
			});
	}

	// Resets the filter by reassigning `filteredData`.
	public setFilter(type: FilterType) {
		this.filteredData = this.filterData(this.myData, type);
	}
	
	private filterData(data: MyData[]) {
		// ... Filter data somehow
		return result;
	}
}
```

The **declarative** way of thinking is "just update the filter that is currently applied", the rest should "react" to it and update accordingly automatically. 

```typescript
// This first step is actually imperative!
public filter = signal<FilterType>('none'); // This can be changed in the template button.
private myData = toSignal(this.myService.getData(this.id));
// This depends on `filter` and `myData`.
private filteredData = computed(() => this.filterData(this.myData(), this.filter()))

private filterData(data: MyData[], filter: FilterType) {
	// Filter data somehow
	return result;
}
```

The alternative to this example with RxJS would be to `next` a `Subject` (or `BehaviorSubject`) with the new filter value. 

> **Keep in mind that this is actually an imperative action**. This ends up being a limitation on how Angular and most frameworks handle reactiveness. They can't be fully 100% declarative. The key point is that **everything after that point** is declarative. 

### So how does it relate to "I want to speak to your manager!"?

If you want to update the filtered data, you don't just try to update the filtered data directly... you talk to its manager. You go above the filtered data and change the thing that it depends on to calculate itself (the imperative thing at the very top of the data flow).