The problem can be divided into two topics:

# Added Boilerplate & Imports

## FastImage
Some elements from external dependencies like `FastImage` have their own typings. So when importing something like `ImageStyle` we are faced with two options... And both options are different **and** they are can't be mixed.

![[Pasted image 20240625100005.png]]

The `style` prop for `FastImage` **needs** to be `ImageStyle` from `react-native-fast-image` **not** from `react-native`. So we need to recast it in the template.

### Example:

```jsx
<FastImage
  style={basketItemStyles.itemImage as ImageStyle}
  source={{
	uri: searchItem.imageUrl,
	cache: FastImage.cacheControl.immutable,
	priority: FastImage.priority.normal,
  }}
  resizeMode={FastImage.resizeMode.cover}
/>
```

## Icon (Element)

The `Icon` element from `react-native-vector-icons` uses the same imports from `react-native` for style props... But uses a different version. I don't know if bumping versions and making it match would solve the issue though.

The style prop expects a `ViewStyle | TextStyle | undefined;` typing but the provided one is `ViewStyle | TextStyle | ImageStyle` creating an error (`Type 'ViewStyle | TextStyle | ImageStyle' is not assignable to type 'StyleProp<TextStyle>'.`)

If you try to cast to the expected type (`ViewStyle | TextStyle | undefined;`), it still fails: `Type 'ViewStyle | TextStyle | undefined' is not assignable to type 'StyleProp<TextStyle>'.`

If casted to just `TextStyle` it fails: `Type 'TextStyle' is not assignable to type 'StyleProp<TextStyle>'.`

If you cast it to `StyleProp<TextStyle>` you get the following error:

```
import("/node_modules/react-native/Libraries/StyleSheet/StyleSheet").StyleProp<import("/node_modules/react-native/Libraries/StyleSheet/StyleSheetTypes").TextStyle>
```

is not assignable to type:

```
import("/node_modules/@types/react-native-vector-icons/node_modules/@types/react-native/index").StyleProp<import("/node_modules/@types/react-native-vector-icons/node_modules/@types/react-native/index").TextStyle>
```

I believe this last one is because of the version mismatch.

So my "solution" for now is using `any`:

![[Pasted image 20240625101016.png]]

Fortunately there aren't many `Icon` elements throughout the app.

### Color style

Most of the time the style applied to the `Icon` will be to change the color of it. Fortunately this can be solved with:

```typescript
color={pickElementForStore({
  '7-ELEVEN': colorVariables.white,
  SPEEDWAY: SpeedwayColors['Neutrals/Black'],
})}
```


## i18n

In several components we create extended hardcoded styles for fontSizes depending on the i18n language like this:

```
fontSize: i18n.language === 'en' ? cartStyles.scanCardText.fontSize : 32,
```

Unfortunately this also needs to change with added boilerplate for typing safety because `phoneNumberText` it typed as `ViewStyle | TextStyle | ImageStyle` and `fontSize` only exists in `TextStyle` so we need to recast it to solve this:

```typescript
const phoneNumberTextStyle = {
	...cartStyles.phoneNumberText,
	fontSize:
	  i18n.language === 'en'
		? (cartStyles.phoneNumberText as TextStyle).fontSize
		: 18,
};
```


# Class consistency between themes

What I wanted to achieve was creating a "safety guard" by having an error raised when a class is created in a secondary theme (like Speedway) that does not exist on the primary theme (7-Eleven)

This was not possible. We can create classes that do not exist on the primary one in the `StyleSheet`.

I settled with this because although we can create those classes, we can not use them:
![[Pasted image 20240625101927.png]]

Unless the class exists first on the primary theme (7-Eleven)


