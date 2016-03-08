# Field Actions

## `actions.focus(model)`
Returns an action object that, when handled by a `formReducer`, changes the `focus` state of the field model in the form to `true`, as well as the corresponding `blur` state to `false`.
The "focus" state indicates that the field model is the currently focused field in the form.

**Arguments:**
- `model`: (String) the model indicated as focused

```js
import {
  actions, getField
} from 'react-redux-form';

// assuming this is a connected component
const Newsletter = (props) => {
  let { newsletterForm, dispatch } = props;
  return <form>
    <input type="email"
      onFocus={() => dispatch(actions.focus('newsletter.email'))} />
    { getField(newsletterForm, 'email').focus &&
      <div>We are focused on emailing you stuff!</div>
    }
  </form>;
}
```

## `actions.blur(model)`
Returns an action object that, when handled by a `formReducer`, changes the `blur` state of the field model in the form to `true`, as well as the corresponding `focus` state to `false`. It also indicates that the field model has been `touched`, and will set that state to `true` and the `untouched` state to `false`.

The "blur" state indicates that the field model is not focused.

**Arguments:**
- `model`: (String) the model indicated as blurred

## `actions.setPristine(model)`
Returns an action object that, when handled by a `formReducer`, changes the `pristine` state of the field model in the form to `true`, as well as the corresponding `dirty` state to `false`.

The "pristine" state indicates that the user has not interacted with this field model yet.

**Arguments:**
- `model`: (String) the model indicated as pristine

## `actions.setDirty(model)`
Returns an action object that, when handled by a `formReducer`, changes the `dirty` state of the field model in the form to `true`, as well as the corresponding `pristine` state to `false`.

The "dirty" state indicates that the model value has been changed.

**Arguments:**
- `model`: (String) the model indicated as dirty

## `actions.setPending(model)`
Returns an action object that, when handled by a `formReducer`, changes the `pending` state of the field model in the form to `true`. It simultaneously sets the `submitted` state to `false`.

This action is useful when asynchronously validating or submitting a model, and is representative of the state between the initial and final state of a field model.

**Arguments:**
- `model`: (String) the model indicated as pending

## `actions.setValidity(model, validity)`
Returns an action object that, when handled by a `formReducer`, changes the `valid` state of the field model in the form to `true` or `false`, based on the `validity` (see below). It simultaneously sets the `errors` on the field model to the inverse of the `validity`.

The `validity` can be an object or a boolean value, indicating which aspects of the field model are valid. A validity object is a key/value pair where the values are functions that return a truthy (if valid) or falsey (if invalid) value.

**Example:**
```js
let val = 'testing123';

dispatch(actions.setValidity('user.password', {
  required: (val) => val && val.length,
  correct: (val) => val === 'hunter2'
}));
// Field model errors will now look like:
// errors: {
//   required: false,
//   correct: true
// }
```
**Arguments:**
- `model`: (String) the model indicated as pending
- `validity`: (Boolean | Object) the validity of the model
