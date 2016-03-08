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

// somewhere with dispatch():
dispatch(actions.setValidity('user.email', true));

// email field:
// {
//   valid: true,
//   validity: true,
//   errors: false
// }

let val = 'testing123';

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
- This action is useful for general-purpose asynchronous validation using callbacks.  If you are using _promises_, using `actions.submit(model, promise)` is a cleaner pattern.

## `actions.setErrors(model, errors)`
Returns an action that, when handled by a `formReducer`, changes the `.valid` state of the field model in the form to `true` or `false`, based on the `errors` (see below). It will also set the `.errors` state of the field model to the `errors`.

It simultaneously sets the `.validity` on the field model to the inverse of the `errors`.

**Arguments**
- `model` _(String)_: the model whose validity will be set
- `errors` _(Boolean | Object | String)_: a truthy/falsey value or an object indicating which error keys of the field model are invalid.

**Example**
```js
import { actions } from 'react-redux-form';

// somewhere with dispatch():
dispatch(actions.setErrors('user.email', true));

// email field:
// {
//   valid: false,
//   validity: false,
//   errors: true
// }

dispatch(actions.setErrors('user.email', 'So many errors!'));

// email field:
// {
//   valid: false,
//   validity: false,
//   errors: 'So many errors!'
// }

let val = 'testing123';

dispatch(actions.setErrors('user.password', {
  empty: !(val && val.length) && 'Password is required!',
  incorrect: val !== 'hunter2' && 'The password is wrong'
}));

// password field:
// {
//   valid: false,
//   errors: { empty: false, incorrect: 'The password is wrong' }
// }
```

**Tips**
- If you aren't hard-coding error messages, use `actions.setValidity(model, validity)` instead. It's a cleaner pattern.
- You can set `errors` to a boolean, object, array, string, etc. Remember: truthy values indicate errors.

## `actions.validateErrors(model, errorValidators)`
Returns an action thunk that calculates the `errors` of the `model` based on the function/object `errorValidators`. Then, the thunk dispatches `actions.setErrors(model, errors)`.

An **error validator** is a function that returns `true` or a truthy value (such as a string) if invalid, and `false` if valid.

**Arguments**
- `model` _(String)_: the model whose validity will be calculated
- `errorValidators` _(Function | Object)_: an error validator _or_ an object whose keys are error keys (such as `'incorrect'`) and values are error validators.

**Example**
```js
import { actions } from 'react-redux-form';
import { isEmail } from 'validator';

// assume user.email = "foo@gmail"

// somewhere with dispatch():
dispatch(actions.validateErrors('user.email', (val) => {
  return !isEmail(val) && 'Not an email!'
}));
// will dispatch actions.setErrors('user.email', 'Not an email!')

dispatch(actions.validateErrors('user.email', {
  notAnEmail: (val) => !isEmail(val) && 'Not an email!',
  unavailable: (email) => email == 'foo@gmail.com' && 'Use a different email'
});
// will dispatch actions.setErrors('user.email', {
//  notAnEmail: 'Not an email!',
//  unavailable: false
// });
```