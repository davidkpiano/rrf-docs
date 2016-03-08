# Form Reducers

A **form reducer** is a reducer that responds to any [field actions](todo). When it receives a field action for a `model`, it updates the field state for that model. If applicable, it will also update the overall form state. Let's say your user model looks like this:

```js
let initialUser = {
  firstName: '',
  lastName: ''
};
```

You can create a form reducer for the `'user'` model like this:

```js
import { formReducer } from 'react-redux-form';

const userFormReducer = formReducer('user', initialUser);
```

Now when you dispatch a field action to the `'user'`, such as `actions.focus('user.firstName')` or `actions.setDirty('user.lastName')`, the `userFormReducer` will update the user form state.

**Important:** The state returned from the form reducer does not contain the model values, apart from the initial model value. This is to keep a clean separation between view (the form) and model values. It also gives you complete control of your model, and allows you to represent it as a plain JavaScript object or array.

### Form Reducers in Stores

Form reducer keys in the store can be named anything that makes sense in your app. I like to name them `'[model]Form'`, such as `'userForm'` to represent the `'user'` model. Here is how you would set up a form reducer in your app's store:

```js
// store.js
import { combineReducers, createStore } from 'redux';
import { formReducer } from 'redux-simple-router';

import { userReducer, initialUserState } from './reducers/user-reducer';

const store = createStore(combineReducers({
  user: userReducer,
  userForm: formReducer('user', initialUserState), // form reducer
  // etc.
}));

export default store;
```

**Important:** Just like the [model reducer](), the `model` of the `formReducer()` creator must point to the _model_ in the store, not the _form_. E.g. the model for `userForm` is `'user'`, not `'userForm'`.