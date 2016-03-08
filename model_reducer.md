# Model Reducer

## `modelReducer(model, [initialState])`
Returns a model reducer that only responds to `change()` and `reset()` actions on the model or model's child values.

**Note:** the `model` _must be the same as_ the key given to the reducer in `combineReducers({...})`.

**Arguments:**
- `model`: (String) the model that the reducer will update
- `initialState`: (Any) the initial state of the model

**Example:**
```js
import { createModelReducer } from 'react-redux-form';
const initialUserState = {
  firstName: '',
  lastName: ''
};
const userReducer = createModelReducer('user', initialUserState);
export default userReducer;
```

## `createFormReducer(model)`
Returns a form reducer that only responds to any actions on the model or model's child values. The reducer's state has the shape of `initialFormState`, with a `fields` property where each field has the shape of `initialFieldState`.

**Arguments:**
- `model`: (String) the model whose form state (and child field states) the reducer will update.

**Example:**

```js
import { createFormReducer } from 'react-redux-form';

const userFormReducer = createFormReducer('user');

export default userFormReducer;
```