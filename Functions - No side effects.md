#clean-code 

# Side effects are lies!

Your function promises to do one thing (because remember [[Functions should do one thing]] and one thing only), but it also does other *hidden* things. Make unexpected changes to a state variable, change a parameter value. In any case **they are devious and damaging mistruths** that often result in strange temporal couplings and order dependencies.

Consider the following code, that checks if a `password` matches the corresponding `user`, and returns `true` or `false`. There is a side effect in there, can you spot it?

```typescript
const checkPassword = (userName: string, password: string): boolean => {
  const user: User = UserGateway.findByName(userName);
  if (!!user) {
    const codedPhrase: string = user.getPhraseEncodedByPassword();
    const phrase: string = this.cryptographer.decrypt(codedPhrase, password);

    if (phrase === 'Valid Password') {
      Session.initialize();
      return true;
    }
  }

  return false;
};
```

The side effect is `Session.initialize()`. The name of the function is `checkPassword`, this name do not imply it will initialize the session. So a developer that trusts the name of this function might call it in the wrong time and accidentally erase the existing session data simply when they try to check the validity of the user. 

This means this function is now tightly coupled to the condition that it can only be called when it is safe to initialize the session. When we have this scenario is important to [[Make meaningful context|provide context]] in the declaration name. We could rename this to `checkPasswordAndInitializeSession`, although this obviously violates the rule [[Functions should do one thing|"Do one thing and one thing only"]] 

# Real life example

Recently I encountered this code block in a merge request. Can you spot the issue?

```typescript
const checkIfPinIsValid = async () => {
  const isCorrectPasscode = await verifyPasscode(pinNumber);
  if (isCorrectPasscode) {
    setPasscodeVerified();
    submit();
  }
  setIsPinValid(isCorrectPasscode);
  setIsLoadingRedemption(false);
};
```

First of all this function is doing multiple things, **and** those things are side effects. Also by the name of the function it implies it should return a `boolean` whether the pin is valid but is returning `void`. 

First of all `verifyPasscode` comes from a util file and `setPasscodeVerified` is in the `rewardsHook`. This goes along with the Angular Karen principle [[Angular - Make sure things never change|"Make sure things never change"]]  which can be discussed afterwards.

It is submitting (side effect), and just by the look of this codeblock we don't know "what" is being submitted, so `submit` might also need a name change. And it is also stopping the loader through `setIsLoadingRedemption`, which is in turn a side effect of the submission function. 