#clean-code #good-practice 

Consider the following functions:
```typescript
getActiveAccount();
getActiveAccounts();
getActiveAccountInfo();
```

How are the programmers in this project supposed to know which of these functions to call?

In the absence of specific conventions, the variable `moneyAmount` is indistinguishable from `money`, `customerInfo` is indistinguishable from `customer`, `accountData` is indistinguishable from `account`, and `theMessage` is indistinguishable from `message`. 

**Distinguish names in such a way that the reader knows what the differences offer.**

What is the difference between `offersData`, `offersInfo`, and `offersList`? These noise words do not provide any meaningful distinction between these variable names. What if instead of that the variable is named `offersAvailable`, or `redeemedOffers`, or `expiredOffers`? These three variables even when they all hold the same information (an `offer`) all of them provide a meaningful distinction of what are the values we expect to find within them. 