#example  #clean-code 

Take the following real life (easy) example:

```typescript
  const handlePress = () => {
    if (pressCounter === 0)
      setTimeout(() => {
        setPressCounter(0);
      }, 2000);

    if (pressCounter === 3) {
      if (ff_isSauceManagingStoreAssociate)
        dispatch(deviceActions.setIsLaunchSauceModalVisible(true));
      else stateActor.send({ type: 'OPEN_STORE_ASSOCIATE' });

      setPressCounter(0);
    }

    setPressCounter((prevState: number) => (prevState = prevState + 1));
  };
```

The name of the function is `handlePress` press of what? Maybe a button yeah, but which button? What is the meaning of pressCounter === 3? What does 3 mean? 

In our codebase we use a ton of `handlePress`, `handleSubmit`, `cancelHandler`, `submitHandler`, etc. All of these, mean pretty much exactly the same, but each one of them does a completely different thing. We need to manually find the context of where, when, how, and what calls this function to know how it will behave. 