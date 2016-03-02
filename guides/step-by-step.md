# Step by Step

This step-by-step guide assumes that you already have a project set up with:

- NPM (with a `package.json` file, though this is optional)
- module importing with Webpack (or another bundler): https://github.com/petehunt/webpack-howto
- ES6 compilation with Babel: http://jamesknelson.com/the-complete-guide-to-es6-with-babel-6/
- Using React with ES6/Babel: http://jamesknelson.com/react-babel-cheatsheet/

Check out the above links if you need any help with those prerequisites.

### 1. Make sure you have the React/Redux dependencies installed.

- `npm install react react-dom --save`
- `npm install redux react-redux --save`

### 2. Install the [redux-thunk](https://github.com/gaearon/redux-thunk) middleware

The `redux-thunk` middleware is useful for determining the proper action to dispatch based on the state, as well as dispatching actions or sequences of actions asynchronously. For many of the utility action creators that React Redux Form provides, `redux-thunk` is necessary.

- `npm install redux-thunk --save`

### 3. Install [react-redux-form](https://github.com/davidkpiano/react-redux-form)

- `npm install react-redux-form --save`

### 4. Setup your app.

```js
import React from 'react';
import ReactDOM from 'react-dom';

import { Provider } from 'react-redux-router';

// We'll create this in Step 5.
import store from './store.js';

// We'll create this in Step 6.
import UserForm from './components/user-form.js';

class App extends React.Component {
  render() {
    return (
      <Provider store={ store }>
        <UserForm />
      </Provider>
    );
  }
}

ReactDOM.render(<App />, document.getElementById('app'));
```

### 5. Setup your store.

Here, you will add the model reducers and form reducers you want to use. As a reminder:

- `createModelReducer(model, initialState)` will create a model reducer, and
- `createFormReducer(model)` will create a form reducer.

A form reducer is _always_ optional, and if you are not concerned with field states such as `focus`, `blur`, `pristine`, `valid`, etc., you can omit it (especially for performance purposes).

**Important:** Make sure that `model` matches the **exact path** to the state. That way, the `<Field>` component will know where to look when connecting the proper state slice with the control components.

```js
// ./store.js
import { createStore, combineReducers } from 'redux';
import {
  createModelReducer,
  createFormReducer
} from 'react-redux-form';

const initialUserState = {
  firstName: '',
  lastName: ''
};

const store = createStore(combineReducers({
  user: createModelReducer('user', initialUserState),
  userForm: createFormReducer('user')
});

export default store;
```

### 6. Setup your form!

```js
import React from 'react';
import { Field, actions } from 'react-redux-form';

class UserForm extends React.Component {
  handleSubmit() {
    let { user, dispatch } = this.props;

    // Do whatever you like in here.
    // You can use redux simple form actions such as:
    // actions.setPending('user', true);
    // actions.setValidity('user.firstName', user.firstName.length > 0);
    // actions.setSubmitted('user', true);
    // etc.
  }
  render() {
    let { user } = this.props;

    return (
      <form onSubmit={() => this.handleSubmit()}>
        <Field model="user.firstName">
          <label>First name:</label>
          <input type="text" />
        </Field>

        <Field model="user.lastName">
          <label>Last name:</label>
          <input type="text" />
        </Field>

        <button type="submit">
          Finish registration, { user.firstName } { user.lastName }!
        </button>
      </form>
    );
  }
}

function mapStateToProps(state) {
  return { user: state.user };
}

export default connect(mapStateToProps)(UserForm);
```