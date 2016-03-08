# Model Reducers

In Redux Simple Form, a **model reducer** is a reducer that responds to `change()` actions on the model you specify when creating the model reducer. Let's say your user model looks like this:

```js
let initialUser = {
  firstName: '',
  lastName: ''
};
```

You can create a model reducer that responds only to changes to models that modify the user like this:

```js
import { modelReducer } from 'react-redux-form';

let initialUser = {
  firstName: '',
  lastName: ''
};

const userReducer = modelReducer('user', initialUser);
```

Now, when you send an `actions.change(...)` action that intends to modify the user, such as `actions.change('user.firstName', 'John')`, the reducer will update the model accordingly.

The `modelReducer(model, initialState)` function takes two arguments: the **model** and the optional **initialState**. It's highly recommended that you provide an initial state to your model reducer. Regardless, the model reducer will create new keys if they don't exist.

## Model Reducers in Stores

The _only requirement_\* is that your model reducer has the **same model as the key of the reducer in the store object.** This is so that the model and field actions know where to get the latest model value. Here is how you would set up a model reducer in your app's store:

```js
// store.js
import { combineReducers, createStore } from 'redux';
import { modelReducer } from 'redux-simple-router';

const store = createStore(combineReducers({
  user: modelReducer('user'),
  items: modelReducer('items'),
  // etc.
}));

export default store;
```

## Updating Models

The model reducer uses the `model` path to know where which part of the state should be updated.

For example, given this state:

```js
const state = {
  user: {  
    firstName: 'John',
    lastName: 'Smith',
    phones: [
      { number: '5551234567', type: 'home' },
      { number: '5559876543', type: 'work' }
    ]
  }
}
```

A value from this object can be retrieved with the path `'user.firstName'` and a value inside an array can be retrieved with `'user.phones[1]'`. You can retrieve deep values as well, e.g. `'user.phones[1].number'`.

For example, to update the second phone number's type, you can `dispatch` a change to its model path:

```js
import { actions } from 'react-redux-form';

// somewhere connect()ed...
dispatch(actions.change('user.phones[1].type', 'mobile'))
```

## Using Existing Reducers

The `modeled()` reducer enhancer gives you the ability to **use your existing reducers** and update them with model changes in React Redux Form. Here's how it works: say you have an existing reducer:

```js
const initialState = {
  firstName: '',
  lastName: '',
  fullName: ''
};

function myReducer(state = initialState, action) {
  switch (actions.type) {
    case 'GET_FULL_NAME':
      return {
        ...state,
        fullName: `${state.firstName} ${state.lastName}`
      };
    default:
      return state;
  }
}
```

If you want your existing reducer to update to actions such as `actions.change('my.firstName', 'David')`, decorate (wrap) it with the `modeled(reducer, model)` function:

```js
import { modeled } from 'react-redux-form';

const initialState = /* ... */

function myReducer(...) {
  /* ... */
}

// Decorated modeled reducer
const myModeledReducer = modeled(myReducer, 'my');

export default myModeledReducer;
```

Now, you will be able to use your existing reducer as-is. The reducer will return the updated state based on model actions such as `actions.change()` and `actions.reset()`, and compose it with the existing reducer:

```js
import { actions } from 'react-redux-form';

let state = { firstName: 'Daniel', lastName: 'Walker' };

let newState = myModeledReducer(state, actions.change('my.firstName', 'Johnnie'));
// => { firstName: 'Johnnie', lastName: 'Walker' }

myModeledReducer(newState, { type: 'GET_FULL_NAME' });
// => { firstName: 'Johnnie', lastName: 'Walker', fullName: 'Johnnie Walker' }
```

Want to know more about reducer enhancers (a.k.a. higher order reducers)? Check out [another example](http://rackt.org/redux/docs/recipes/ImplementingUndoHistory.html) of a higher-order reducer in the Redux ecosystem.

## Custom Model Reducers

Using `modelReducer()` or `modeled()` is *not required*, and you can implement your own functionality to respond to model changes:

```js
import { actionTypes } from 'react-redux-form';

const userReducer = (state = {}, action) => {
  if (action.type === actionTypes.CHANGE) {
    if (action.model === 'user.firstName') {
      return {
        ...state,
        firstName: action.value
      }
    }
  }

  return state;
}
```

However, `createModelReducer()` is _really convenient_, as it uses [icepick](https://github.com/aearly/icepick) to efficiently update the model given a model string such as `"foo.bar[2].baz"`.