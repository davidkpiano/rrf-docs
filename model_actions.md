# Model Action Creators

All model and field actions can be imported via `import { actions } from 'react-redux-form'`.

## `actions.change(model, value)`

Returns an action that, when handled by a `modelReducer`, changes the value of the `model` to the `value`.

When the change action is handled by a `formReducer`, the field model's `.dirty` state is set to `true` and its corresponding `.pristine` state is set to `false`.

**Arguments**
- `model` _(String)_: the model whose value will be changed
- `value` _(any)_: the value the model will be changed to


**Example**
```js
import {
  modelReducer,
  actions
} from 'react-redux-form';

const userReducer = modelReducer('user');

let initialState = { name: '', age: 0 };

userReducer(initialState, actions.change('user.name', 'Billy'));
// => { name: 'Billy', age: 0 }
```

**Tips**
- The `model` path can be as deep as you want. E.g. `actions.change('user.phones[0].type', 'home')`

## `actions.reset(model)`
Returns an action object that, when handled by a `modelReducer`, changes the value of the respective model to its initial value.

**Arguments:**
- `model` _(String)_: the model whose value will be reset to its initial value.

```js
import {
  modelReducer,
  actions
} from 'react-redux-form';

let initialState = { count: 10 };

const counterReducer = modelReducer('counter', initialState);

let nextState = counterReducer(initialState,
  actions.change('counter.count', 42));
// => { count: 42 }

let resetState = counterReducer(nextState,
  actions.reset('counter.count'));
// => { count: 10 }
```