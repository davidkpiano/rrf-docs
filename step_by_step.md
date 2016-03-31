# Quick Start

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

- `modelReducer(model, initialState)` will create a model reducer, and
- `formReducer(model)` will create a form reducer.

Form reducers are _always_ optional. If you are not concerned with field states such as `focus`, `blur`, `pristine`, `valid`, etc., you can omit it (especially for performance purposes).

**Important:** Make sure that `model` matches the **exact path** to the state. This will let the various model and form actions know where to retrieve the model value from the store.

```js
// ./store.js
import { createStore, combineReducers } from 'redux';
import {
  modelReducer,
  formReducer
} from 'react-redux-form';

const initialUserState = {
  firstName: '',
  lastName: ''
};

const store = createStore(combineReducers({
  user: modelReducer('user', initialUserState),
  userForm: formReducer('user', initialUserState)
});

export default store;
```

### 6. Setup your form!

```js
import React from 'react';
import { connect } from 'react-redux';
import { Field, Form, actions } from 'react-redux-form';

class UserForm extends React.Component {
  handleSubmit(user) {
    let { dispatch } = this.props;

    // Do whatever you like in here.
    // You can use actions such as:
    // dispatch(actions.submit('user', somePromise));
    // etc.
  }
  render() {
    let { user } = this.props;

    return (
      <Form model="user"
        onSubmit={(user) => this.handleSubmit(user)}>
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
      </Form>
    );
  }
}

function mapStateToProps(state) {
  return { user: state.user };
}

export default connect(mapStateToProps)(UserForm);
```

<div id="root"></div>