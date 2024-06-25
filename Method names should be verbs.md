#good-practice  #clean-code 

Method names should be verbs or verb phrases. Examples of them are:

* `postPayment`
* `saveClient`
* `deleteTransaction`
* `saveTransaction`


**Accessors**: Should be named for their value and prefixed with `get`.
**Mutators**: Should be prefixed with `set`.
**Predicates**: Should be prefixed with `is`.

Example:

```typescript
const name = employee.getName();
const customer = new Customer();
customer.setName('John Doe');
if (paycheck.isPosted()) {
	// Then ...
}
```

