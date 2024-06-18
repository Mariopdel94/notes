#clean-code

Notes:
* A very big part of this CoP could be code review of existing code from members
* Shoot for getting one person from each team to be a part of this CoP

# Clean Code

[[Small things matter]]

> The smallest bit of sloppy construction, of the door that does not close tightly or the slightly crooked tile on the floor, or even the messy desk, completely dispels the charm of the larger whole. That is what clean code is about.

[[Maintenance over producing]]
When we spend more of our time maintaining the codebase, we should focus more on how to facilitate it than focusing on "creating" new code. 

# Angular specific

 Convert **imperative** code to **reactive** code.

Some of the key ides you can use to convert imperative code to a more reactive pattern are two *Karen* inspired rules you can follow to get more into that declarative/reactive pattern mindset:

1. Make sure things never change
2. "I want to speak to your manager"

  ## Imperative approach
  ![[Pasted image 20240618114222.png]]
The key problem is that we have these variables that hold values,
and we change those by reassigning them imperatively throughout
the life of the application

For example, we might have a class member to hold some data, which might be `undefined` initially or in this case just an empty array 

```typescript
export class MyComponent {
	private myData: MyData[] = [];
}
```

We fetch that data in the `ngOnInit` hook and then change the value of the `myData` class member by reassigning it.

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

This is exactly what I mean by "not changing things". Reassigning the value of this class member is what makes it **imperative**.

How we fetch/update this data does not matter, it could be an observable, a promise. it could even by synchronous or anything else. **The problem is reassigning the value of the class member.** 

This can cause a snowball effect, because now if we have something derived from that data, we would also need to make sure to always remember to update that as well.

```typescript

```