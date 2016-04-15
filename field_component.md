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

It can also be a function that returns a string model. It is called with one argument:
- `state` _(object)_: the entire state returned from `store.getState()`.

```js
// assuming store state similar to:
// {
//   users: [ ... ],
//   currentUser: 3
// }

function getCurrentUserName(state) {
  const currentUserIndex = state.currentUser;
  
  return `users.${currentUserIndex}.name`;
}

// in component's render() method
<Field model={getCurrentUser}>
  <input type="text" />
</Field>
```

### `updateOn` property
A string specifying when the component should dispatch a `change(...)` action, with one of these values:

- `"change"` - will dispatch in `onChange`
- `"blur"` - will dispatch in `onBlur`
- `"focus"` - will dispatch in `onFocus`

So, `<Field model="foo.bar" updateOn="blur">` will only dispatch the `change(...)` action on blur.

**Tips**
- Use `changeAction` if you want to dispatch custom actions along with the `actions.change(...)` action.

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

### `validateOn` property
A string specifying when validation should occur. By default, validation happens on `"change"`. The `validateOn` property can have these values:
- `"change"` (default) - validate on the `onChange` event handler
- `"blur"` - validate on the `onBlur` event handler
- `"focus"` - validate on the `onFocus` event handler

**Tips**
- Validation will always occur **on load**; i.e., when the component is mounted. This is to ensure an accurate validation state for a new form.
- To avoid displaying error messages on load (as fields might be invalid), use the `.pristine` property of the field when conditionally showing error messages.

### `asyncValidators` property
A map where the keys are validation keys, and the values are the corresponding functions that (asynchronously) determine the validity of each key, given the model's value.

Each async validator function is called with 2 arguments:
- `value` - the model value
- `done(validity)` - a callback function that should be called with the calculated validity

For example, this field validates that a username is available via a promise:

```js
// function that returns a promise
import isAvailable from '../path/to/is-available';

<Field model="user.username"
  asyncValidators={{
    isAvailable: (value, done) => {
      isAvailable(value)
        .then((result) => done(result));
    }
  }}>
  <input type="text" />
</Field>
```

**Tips**
- 

### `parser` property
A function that _parses_ the view value of the field before it is changed. It takes in two arguments:
- `value` - the view value that represents the _next_ model value
- `previous` (optional) - the current model value _before_ it is changed

**Example**
```js
function toAge(value) {
  return parseInt(value) || 0;
}

<Field model="user.age"
  parser={ toAge }>
  <input type="number" />
</Field>
```

### `changeAction` property
An action creator (function) that specifies which action the `<Field>` component should use when dispatching a change to the model. By default, this action is:

- `actions.change(model, value)` for text input controls
- `actions.toggle(model, value)` for checkboxes (single-value models)
- `actions.xor(model, value)` for checkboxes (multi-value models)

The action creator takes in two arguments:

- `model` - the model that is being changed
- `value` - the value that the model is being changed to

**Example**

To create a custom `<Field>` that submits the form on blur:

```js
import { actions } from 'react-redux-form';

const submitPromise = ... // a promise

function changeAndSubmit(model, value) {
  return (dispatch) => {
    dispatch(actions.change(model, value));
    dispatch(actions.submit('user', submitPromise));
  };
}

// Then, in your <Field> components...
<Field model="user.name"
  changeAction= { changeAndSubmit }
  updateOn="blur">
  <input type="email" />
</Field>
```

**Tips**
- Use `changeAction` to do any other custom actions whenever your value is to change.
- Since `changeAction` expects an action creator and `redux-thunk` is used, you can asynchronously dispatch actions (like the example above).