# Guide for React experimental debugger

1. Build the app as normal, following the [Getting started instructions](https://digital-711.atlassian.net/wiki/spaces/RNDDOC/pages/3297738854/Getting+started+with+TaBaSCO)
2. Stop the metro terminal running after running `npm run android`
3. Run `npm run startWithDebugger` (aliased script for `npx react-native start --experimental-debugger`) You should see this on your terminal:

	![[Pasted image 20240625124222.png]]

Press `j` in this terminal and the debugger should pop up.
# React Devtools

If you need further debugging tools use the [React Devtools]((https://reactnative.dev/docs/next/react-devtools) for this 

1.  Run `adb reverse tcp:8097 tcp:8097` to forward port 8097 for devtools.
2. In a separate terminal run `npx react-devtools`




