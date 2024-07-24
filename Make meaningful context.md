#clean-code  #good-practice 

Image you have variables like `firstName`, `lastName`, `street`, `houseNumber`, `city`, `state`, and `zipCode`. Of course you know that these variables taken together are meant to be used for an address. But what if you use the variable `state` alone? Would you automatically infer that is part of an address? 

A "fast" solution would be to add a prefix to the variable names `addrStreet`, `addrCity`, `addrState`, `addrZipCode`, etc. But to be honest this is a half-baked solution, because this just adds unnecessary clutter to the code and. makes it harder to read. Plus if you are like me, with a bit of [Dyslexia]([dyslexia definition - Google Search](https://www.google.com/search?q=dyslexia+definition&oq=dislexia+&gs_lcrp=EgZjaHJvbWUqEAgBEAAYkQIYsQMYgAQYigUyBggAEEUYOTIQCAEQABiRAhixAxiABBiKBTIQCAIQABiRAhixAxiABBiKBTIQCAMQABiRAhixAxiABBiKBTINCAQQABiRAhiABBiKBTIJCAUQABgKGIAEMgkIBhAAGAoYgAQyDAgHEAAYFBiHAhiABDIHCAgQABiABNIBCDQ0ODRqMGo5qAIAsAIB&sourceid=chrome&ie=UTF-8))  then you could even mistakenly read `addStreet` and [[Method names should be verbs|confuse it with a method because of the **add** verb]].


The real solution would be to create an `Address` class/interface/type. Now not only the reader but the IDE knows that this variable is part of a bigger concept. 

```typescript
const client: Client & Person = {
	firstName: 'Mario',
	lastName: 'Pineda',
	address: <Partial<Address>>{
		street: '123 North Street',
		state: 'TX',
	}
};
```

Now you can access `this.client.address.state` and is clear the intent, and the context of this variable.

