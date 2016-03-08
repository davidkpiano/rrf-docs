# Validation Action Creators

## `actions.setValidity(model, validity)`
Returns an action that, when handled by a `formReducer`, changes the `.valid` state of the field model in the form to `true` or `false`, based on the `validity` (see below). It will also set the `.validity` state of the field model to the `validity`.

It simultaneously sets the `.errors` on the field model to the inverse of the `validity`.

**Arguments**
- `model` _(String)_: the model whose validity will be set
- `validity` _(Boolean | Object)_: a boolean value or an object indicating which validation keys of the field model are valid.

**Example**
```js
import { actions } from 'react-redux-form';

let val = 'testing123';

// somewhere with dispatch():
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


## `actions.validate(model, validators)`
Returns an action thunk that calculates the `validity` of the `model` based on the function/object `validators`. Then, the thunk dispatches `actions.setValidity(model, validity)`.

A **validator** is a function that returns `true` if valid, and `false` if invalid.

**Arguments**
- `model` _(String)_: the model whose validity will be calculated
- `validators` _(Function | Object)_: a validator function _or_ an object whose keys are validation keys (such as `'required'`) and values are validators.

**Example**
```js
import { actions } from 'react-redux-form';
import { isEmail } from 'validator';

// assume user.email = "foo@gmail"

// somewhere with dispatch():
dispatch(actions.validate('user.email', isEmail));
// will dispatch actions.setValidity('user.email', false)

dispatch(actions.validate('user.email', {
  isEmail,
  available: (email) => email !== 'foo@gmail.com'
});
// will dispatch actions.setValidity('user.email', {
//  isEmail: false,
//  available: true
// });
```

## `actions.asyncSetValidity(model, asyncValidator)`
Returns an action thunk that calculates the `validity` of the `model` based on the async function `asyncValidator`. That function dispatches `actions.setValidity(model, validity)` by calling `done(validity)`.

**Arguments**
- `model` _(String)_: the model whose validity will asynchronously be set
- `asyncValidator(value, done)` _(Function)_: a function that is given two arguments:
  - `value` - the value of the `model`
  - `done` - the callback where the calculated `validity` is passed in as the argument.

**Example**
```js
import { actions } from 'react-redux-form';

// async function
function isEmailAvailable(email, done) {
  fetch('...', { body: email })
    .then((response) => {
      done(response); // true or false
    });
}

// somewhere with dispatch():
dispatch(actions.asyncSetValidity('user.email', isEmailAvailable));
// => 1. will set .pending to true, then eventually...
// => 2. will set .validity to response and .pending to false
```

**Tips**
- This action is useful for general-purpose asynchronous validation.  If you are using _promises_, using `actions.submit(model, promise)` is a cleaner pattern.