# Field Component

## `<Field>...</Field>`

```js
import { Field } from 'react-redux-form';

// inside a component's render() method ...
<Field model="user.name">
  <input type="text" />
</Field>

// a required field
<Field model="user.email"
  updateOn="blur"
  validators={{
    required: (val) => val && val.length
  }}>
  <input type="email" />
</Field>
```

The `<Field />` component is a helper component that wraps one or more controls and maps actions for the specified `model` to events on the controls. The supported controls are:

- `<input />` - any text type
- `<textarea />`
- `<input type="checkbox" />`
- `<input type="radio" />`
- `<select />`
- `<select multiple/>`

The only required prop is `model`, which is a string that represents the model value in the store.

**Props**
- `model` _(String)_: the string representing the store model value
- `updateOn` _(String | Function)_: a function that decorates the default `onChange` function, or a string:
  - `"change"` (default)
  - `"blur"`
  - `"focus"`
- `validators` _(Object)_: a validation object with key-validator pairs (see below)
- `validateOn` _(String)_: a string that indicates when to validate the field:
  - `"change"` (default), `"blur"`, or `"focus"`
- `asyncValidators` _(Object)_: an async validation object with key-asyncValidator pairs (see below)
- `asyncValidateOn` _(String)_: a string that indicates when to validate the field:
  - `"change"`, `"blur"` (default), or `"focus"`
- `errors` _(Object)_: like `validators`, an error validation object with key-errorValidator pairs. The inverse of `validators`.
- `parser` (Function) - a function that parses the value before updating the model.

## Field Component properties

### `model` property

The string representing the model value in the store.
```js
// in store.js
export default createStore(combineReducers({
  'user': createModelReducer('user', { name: '' })
}));


// in component's render() method
<Field model="user.name">
  <input type="text" />
</Field>
```

### `updateOn` property
A string or function specifying when the component should dispatch a `change(...)` action. If a string, `updateOn` can be one of these values:

- `"change"` - will dispatch in `onChange`
- `"blur"` - will dispatch in `onBlur`
- `"focus"` - will dispatch in `onFocus`

So, `<Field model="foo.bar" updateOn="blur">` will only dispatch the `change(...)` action on blur.

If `updateOn` is a function, the function given will be called with the `change` action creator. The function given will be called in `onChange`. For example:

```js
import debounce from 'lodash/debounce';
<Field model="test.bounce"
  updateOn={(change) => debounce(change, 1000)} >
  <input type="text" />
</Field>
```

**Tips**
- You can use `updateOn` to dispatch custom actions along with the `actions.change(...)` action.

### `validators` property
A map where the keys are validation keys, and the values are the corresponding functions that determine the validity of each key, given the model's value.

For example, this field validates that a username exists and is longer than 4 characters:

```js
<Field model="user.username"
  validators={{
    required: (val) => val.length,
    length: (val) => val.length > 4
  }}>
  <input type="text" />
</Field>
```

**Tips**
- Always prefer `validators=..."` over `"errors=..."`. Even though validation will run on _both_, the `validators` pattern is much cleaner.
- Only use the `errors` prop if you intend to hard-code error messages in your component.
- If using ES2015 and you have validator functions, you can do this destructuring shortcut:

```js
const required = (val) => val && val.length;
const length = (val) => val.length > 8;

<Field model="user.username"
  validators={{ required, length }}>
  <input type="text">
</Field>
```
