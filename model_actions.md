# Model Action Creators

All model and field action creators can be imported via `import { actions } from 'react-redux-form'`. The action thunk creators require [redux-thunk-middleware](https://github.com/gaearon/redux-thunk) to work, as they use thunks to determine the current model state. 

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
Returns an action that, when handled by a `modelReducer`, changes the value of the respective model to its initial value.

**Arguments**
- `model` _(String)_: the model whose value will be reset to its initial value.

**Example**
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

**Tips**
- This action will reset both the model value in the model reducer, _and_ the model field state in the form reducer (if it exists).
- To reset just the field state (in the form reducer), use `actions.setInitial(model)`.

## `actions.merge(model, values)`
Dispatches an `actions.change(...)` action that merges the `values` into the value specified by the `model`.

**Arguments**
- `model` _(String)_: the model to be merged with `values`.
- `values` _(Object | Object[] | Objects...)_: the values that will be merged into the object represented by the `model`.

**Tips**
- Use this action to update multiple and/or deep properties into a model, if the model represents an object.
- This uses `icepick.merge(modelValue, values)` internally.


## `actions.xor(model, item)`
Dispatches an `actions.change(...)` action that applies an "xor" operation to the array represented by the `model`; that is, it "toggles" an item in an array.

If the model value contains `item`, it will be removed. If the model value doesn't contain `item`, it will be added.

**Arguments**
- `model` _(String)_: the array model where the `xor` will be applied.
- `item` _(any)_: the item to be "toggled" in the model value.

**Example**

```js
import { actions } from 'react-redux-form';

// assume user.numbers = [1, 2, 3, 4, 5]

dispatch(actions.xor('user.numbers', 3));
// user.numbers = [1, 2, 4, 5]

dispatch(actions.xor('user.numbers', 6));
// user.numbers = [1, 2, 4, 5, 6]
```

**Tips**
- This action is most useful for toggling a checkboxes whose values represent items in a model's array.

## `actions.push(model, item)`
Dispatches an `actions.change(...)` action that "pushes" the `item` to the array represented by the `model`.

**Arguments**
- `model` _(String)_: the array model where the `item` will be pushed.
- `item` _(any)_: the item to be "pushed" in the model value.

**Tips**
- This action does not mutate the model. It only simulates the mutable `.push()` method.


## `actions.toggle(model)`
Dispatches an `actions.change(...)` action that sets the `model` to true if it is falsey, and false if it is truthy.

**Arguments**
- `model` _(String)_: the model whose value will be toggled.

**Tips**
- This action is most useful for single checkboxes.


## `actions.filter(model, iteratee)`
Dispatches an `actions.change(...)` action that filters the array represented by the `model` through the `iteratee` function.

If no `iteratee` is specified, the identity function is used by default.

**Arguments**
- `model` _(String)_: the array model to be filtered.
- `iteratee` _(Function)_: the filter iteratee function that filters the array represented by the model.
  - default: `identity` (`a => a`)


## `actions.map(model, iteratee)`
Dispatches an `actions.change(...)` action that maps the array represented by the `model` through the `iteratee` function.

If no `iteratee` is specified, the identity function is used by default.

**Arguments**
- `model` _(String)_: the array model to be mapped.
- `iteratee` _(Function)_: the map iteratee function that maps the array represented by the model.


## `actions.remove(model, index)`
Dispatches an `actions.change(...)` action that removes the item at the specified `index` of the array represented by the `model`.

**Arguments**
- `model` _(String)_: the array model to be updated.
- `index` _(Number)_: the index that should be removed from the array.
