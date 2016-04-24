# Form Reducer

## `formReducer(model, [initialState])`
Returns a form reducer that only responds to any actions on the model or model's child values. The reducer's state has the shape of `initialFormState`, with a `fields` property where each field has the shape of `initialFieldState`.

If provided an `initialState`, the form reducer will initialize its fields based on the `initialState`.

**Arguments**
- `model` _(String)_: the model whose form state (and child field states) the reducer will update.
- `initialState` _(any)_: the initial state of the model

**Example**

```js
import { formReducer } from 'react-redux-form';

const initialUserState = {
  firstName: '',
  lastName: ''
};

const userFormReducer = createFormReducer('user', initialUserState);


let formState = userFormReducer(undefined,
  actions.change('user.firstName', 'Bob'));

formState.fields.firstName;
// => { touched: true, pristine: false, dirty: true, ... }
```

**Tips**
- It's a good idea to always provide the `initialState` for the form reducer. That way, you don't have to check if a field exists before trying to access it.
- If a field doesn't exist yet (because it wasn't initialized), you can either:
  - check that it exists; if not, return `initialFieldState` (which you can import)
  - or just `getField(someForm, 'model.string')` which does the above.

## `getField(formState, model)`
Returns the field from the `formState` if the field exists (has been initialized).

If the field doesn't exist, the `initialFieldState` is returned.

**Arguments**
`formState` _(Object)_: the form state as returned by the form reducer.
`model` _(String)_: the string model path to the field inside the form model, if it exists.

**Example**
```js
// Assuming userForm is a form state, where the
// 'user.foobar' field does not exist yet

userForm.fields.foobar.valid;
// => will throw error

getField(userForm, 'foobar').pristine;
// => true (uninitialized - no error)
```

## `initialFieldState`

All initialized fields are set to this initial field state:

```js
const initialFieldState = {
  blur: true, // will be deprecated
  dirty: false, // will be deprecated
  focus: false,
  pending: false,
  pristine: true,
  submitted: false,
  submitFailed: false,
  retouched: false,
  touched: false,
  untouched: true, // will be deprecated
  valid: true,
  validating: false,
  viewValue: null,
  validity: {},
  errors: {},
};
```

If an `initialState` (model state) is passed in as the second argument to `formReducer(model, initialState)`, then the `.initialValue` prop will be part of that initial field state.

**Example**
```js
import { formReducer } from 'react-redux-form';

const initialUserState = {
  firstName: '',
  lastName: ''
};

const userFormReducer = createFormReducer('user', initialUserState);

// userForm.fields.firstName now contains:
// { ...
//   initialValue: '',
//   ...
// }
```