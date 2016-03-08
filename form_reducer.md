# Form Reducer

## `formReducer(model, [initialState])`
Returns a form reducer that only responds to any actions on the model or model's child values. The reducer's state has the shape of `initialFormState`, with a `fields` property where each field has the shape of `initialFieldState`.

If provided an `initialState`, the form reducer will initialize its fields based on the `initialState`.

**Arguments**
- `model` _(String)_: the model whose form state (and child field states) the reducer will update.
- `initialState` _(any)_: the initial state of the model

**Example**

```js
import { formReducer } from 'react-redux-form';

const initialUserState = {
  firstName: '',
  lastName: ''
};

const userFormReducer = createFormReducer('user', initialUserState);


let formState = userFormReducer(undefined,
  actions.change('user.firstName', 'Bob'));

formState.fields.firstName;
// => { touched: true, pristine: false, dirty: true, ... }
```

**Tips**
- It's a good idea to always provide the `initialState` for the form reducer. That way, you don't have to check if a field exists before trying to access it.
- If a field doesn't exist yet (because it wasn't initialized), you can either:
  - check that it exists; if not, return `initialFieldState` (which you can import)
  - or just `getField(someForm, 'model.string')` which does the above.