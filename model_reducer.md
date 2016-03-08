# Model Reducer

## `modelReducer(model, [initialState])`
Returns a model reducer that only responds to `change()` and `reset()` actions on the model or model's child values.

**Note:** the `model` _must be the same as_ the key given to the reducer in `combineReducers({...})`.

**Arguments**
- `model` _(String)_: the model that the reducer will update
- `initialState` _(any)_: the initial state of the model

**Example:**
```js
import { modelReducer } from 'react-redux-form';
const initialUserState = {
  firstName: '',
  lastName: ''
};
const userReducer = createModelReducer('user', initialUserState);
export default userReducer;
```

```js
// in your store:
import { createStore, applyMiddleware, combineReducers } from 'redux';
import thunk from 'redux-thunk';
import { modelReducer } from 'react-redux-form';

import userReducer from '../reducers/user-reducer';

const store = applyMiddleware(thunk)(createStore)(combineReducers({
  user: userReducer,
  team: modelReducer('team', [])
});
```

## `formReducer(model, [initialState])`
Returns a form reducer that only responds to any actions on the model or model's child values. The reducer's state has the shape of `initialFormState`, with a `fields` property where each field has the shape of `initialFieldState`.

**Arguments**
- `model` _(String)_: the model whose form state (and child field states) the reducer will update.
- `initialState` _(any)_: the initial state of the model state. This is usually the same as the `initialState` passed into the `modelReducer()`.

**Example**

```js
import { formReducer } from 'react-redux-form';

const initialUserState = {
  firstName: '',
  lastName: ''
};

const userFormReducer = createFormReducer('user', initialUserState);

export default userFormReducer;
```

**Tips**
- You might not need a form reducer if you don't care about field state (such as `blur`, `focus`, `pristine`, `touched`, etc.) or validation. If this is the case, don't create a form reducer. You'll save on performance costs.
- Fields in the form state can be accessed from `<form>.fields`, which is a flat object.
- E.g., the `user.email` model is retrieved from `userForm.fields.email`, whereas the `user.phones[3].type` model is retrieved from `userForm.fields['phones.3.type']`, and _not_ `userForms.fields.phones[3].type`.
  - Why? For performance, simplicity, and integrity. If you have a model named `user.focus` or `user.valid`, you won't have any collisions with the flat `<form>.fields[<field>]` shape.