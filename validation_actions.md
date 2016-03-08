# Validation Action Creators

## `actions.setValidity(model, validity)`
Returns an action that, when handled by a `formReducer`, changes the `.valid` state of the field model in the form to `true` or `false`, based on the `validity` (see below). It will also set the `.validity` state of the field model to the `validity`.

It simultaneously sets the `.errors` on the field model to the inverse of the `validity`.

**Arguments**
- `model` _(String)_: the model whose validity will be set
- `validity` _(Boolean | Object)_: a boolean value or an object indicating which validation keys of the field model are valid.

**Example**
```js
let val = 'testing123';

dispatch(actions.setValidity('user.email', true));

// email field:
// {
//   valid: true,
//   validity: true,
//   errors: false
// }

dispatch(actions.setValidity('user.password', {
  required: val && val.length,
  correct: val === 'hunter2'
}));

// password field:
// {
//   valid: false,
//   validity: { required: true, correct: false },
//   errors: { required: false, correct: true }
// }
```

**Tips**
- If you _really_ want to set error messages instead, use `actions.setErrors(model, errors)`.
- Since arrays are objects, the `validity` argument _can_ be an array. Only do this if your use case requires it.


