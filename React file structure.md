#react #good-practice 

Organize a React Native file this way:

- State variables
- Selectors
- Hooks
- Local variables
- Local functions/methods
- Effects
- Additional view blocks
- Main view block

## Example:

![[react-file-structure.png]]

Each section should be separated by a single empty line

> *Note*: Comments naming each section were added just for example, preferably the comments should not be there. Why? Because [[The best comment is no comment at all]]

## Why organize the file?
So the component be more declarative portraying the parts and pieces of the component in a quick glance

## Tip for VSCode users:
> **Shortcut**: `Cmd + K and then -` collapses everything in the file (giving you the view in the example screenshot) using `+` instead expands everything. If the cursor is inside a nested scope it will collapse/expand everything except where the cursor is.