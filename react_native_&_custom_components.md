# React Native & Custom Components

React Redux Form supports the use of React Native form controls and 3rd-party components. Under the hood, all `<Field>` does is map actions bound to the dispatcher with their appropriate props, such as `actions.blur(...)` for the `onBlur` prop. However, not all form controls have standard event handler props such as `onChange`, `onFocus`, and `onBlur`. Thankfully, with `createFieldClass()`, you can create adapters to map those standard event handlers to the custom event handler props.

## [React Native](http://facebook.github.io/react-native/)

The following React Native form components are fully supported, so you can freely use the following components with `<Field>` from `react-redux-form/lib/native`, without any extra configuration.

- [`DatePickerIOS`](https://facebook.github.io/react-native/docs/datepickerios.html#content)
- [`MapView`](https://facebook.github.io/react-native/docs/mapview.html#content)
- [`Picker`](https://facebook.github.io/react-native/docs/picker.html#content)
- [`PickerIOS`](https://facebook.github.io/react-native/docs/pickerios.html#content)
- [`SegmentedControlIOS`](https://facebook.github.io/react-native/docs/segmentedcontrolios.html#content)
- [`SliderIOS`](https://facebook.github.io/react-native/docs/sliderios.html#content)
- [`Switch`](https://facebook.github.io/react-native/docs/switch.html#content)
- [`TextInput`](https://facebook.github.io/react-native/docs/textinput.html#content)

```js
import { Field } from 'react-redux-form/lib/native';

// in your component's render() method:

// DatePickerIOS*
<Field model="user.date">
  <DatePickerIOS mode="date" />
</Field>

// MapView*
<Field model="user.region" updateOn="blur">
  <MapView />
</Field>

// Picker and PickerIOS
<Field model="user.language">
  <PickerIOS>
    <PickerIOS.Item label="Java" value="Java" />
    <PickerIOS.Item label="JavaScript" value="JavaScript" />
    <PickerIOS.Item label="Haskell" value="Haskell" />
    <PickerIOS.Item label="Scala" value="Scala" />
  </PickerIOS>
</Field>

// SegmentedControlIOS
<Field model="user.choice">
  <SegmentedControlIOS values={['One', 'Two', 'Three']} />
</Field>

// SliderIOS
<Field model="user.slider">
  <SliderIOS step={1} />
</Field>

// Switch*
<Field model="user.switch">
  <Switch value={ user.switch }/>
</Field>

// TextInput
<Field model="user.name">
  <TextInput />
</Field>
```

- For `DatePickerIOS`, you might get `Warning: Failed propType` warnings. This is innocuous and related to [this React issue](https://github.com/facebook/react/issues/4494), and can be safely ignored. The required props are provided in `React.cloneElement()` behind the scenes.
- For best performance in `MapView`, it's recommended to update the value on `blur` by setting `updateOn="blur"`, which maps the `change()` action to `onRegionChangeComplete`.
- The `Switch` component currently requires the `value` prop to be set on the switch. Within a connected component, this is just a matter of setting it to the same value as the `model` value.

To see all the React Native prop mappings, [view the source code](https://github.com/davidkpiano/react-redux-form/blob/master/src/native/index.js).

## Custom Components
It is straightforward to map React-Redux-Form's actions to event handlers on any kind of custom component. **This is the recommended way** to handle custom components, as issues may arise when components do not have a `.displayName` (this is how RRF recognizes components).

```js
import React from 'react';
import { connect } from 'react-redux';
import { actions } from 'react-redux-form';

// existing custom component
import CustomInput from '../path/to/custom-input-component';

// wrapper field
class MyCustomInput extends React.Component {
  render() {
    let { model, dispatch } = this.props;
    
    return (
      <CustomInput
        onCustomChange={e => dispatch(actions.onChange(model, e))}
      >
    );
  }
}

export default connect(s => s)(CustomField);

// Usage:
<MyCustomInput model="user.name" />
```

For all other custom and 3rd-party components that properly have a `displayName`, custom `<Field>` adapters can be created using `createFieldClass()` to map standard props to custom props.

The `createFieldClass()` function accepts one argument: a component-prop `{ key: value }` mapping where the:

- `key` is the component name, and the
- `value` is a function that takes in the _existing_ prop mapping and returns a _new_ prop mapping.

For example, the React Native `<PickerIOS />` component has an `onValueChange` event handler prop and a `selectedValue` prop. We want to map these "custom" event handler props to behave like the standard props:

- `onValueChange` should be mapped to `onChange`
- `selectedValue` should be mapped to `value`

This is all done in the component-prop mapping:

```js
import { createFieldClass } from 'react-redux-form';

const NativeField = createFieldClass({
  'PickerIOS': (props) => ({
    onValueChange: props.onChange,
    selectedValue: props.modelValue
  })
});

// in the component's render() method:
<NativeField model="...">
  <PickerIOS>
    ...
  </PickerIOS>
</NativeField>
```

Now, if you know for certain that a custom component has the same property mappings as an existing native (DOM) control, the `controls` component-props mapping can be imported for convenience, which contains the following mappings:

- `"text"` for `<input type="text" />` and all other text-based `<input />` elements (like `type="color"`, etc.)
- `"textarea"` for `<textarea />`
- `"checkbox"` for `<input type="checkbox" />`
- `"radio"` for `<input type="radio" />`
- `"select"` for `<select> ... </select>`

For example, in [material-ui](http://www.material-ui.com/#/), the `TextField` component has the same event handler props as `<input type="text" />` (`onChange`, `onBlur`, `onFocus`, etc.), so mapping is straightforward with `controls`:

```js
import { createFieldClass, controls } from 'react-redux-form';
import TextField from 'material-ui/lib/text-field';

const MaterialField = createFieldClass({
  'TextField': controls.text
});

// render():
<MaterialField model="...">
  <TextField />
</MaterialField>
```