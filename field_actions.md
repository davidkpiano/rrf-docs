# Field Action Creators

All model and field actions can be imported via `import { actions } from 'react-redux-form'`.

## `actions.focus(model)`
Returns an action that, when handled by a `formReducer`, changes the `.focus` state of the field model in the form to `true`.

The "focus" state indicates that the field model is the currently focused field in the form.

**Arguments**
- `model` _(String)_: the model indicated as focused

```js
import { actions } from 'react-redux-form';

// in a connect()-ed component:
const Newsletter = (props) => {
  let { newsletterForm, dispatch } = props;
  
  return <form>
    <input type="email"
      onFocus={() => dispatch(actions.focus('newsletter.email'))} />
    { newsletterForm.fields.email.focus &&
      <div>Sweet, you're signing up!</div>
    }
  </form>;
}
```

## `actions.blur(model)`
Returns an action that, when handled by a `formReducer`, changes the `.focus` state to `false`. It also indicates that the field model has been `.touched`, and will set that state to `true`.

A "blurred" field indicates that the field model control is not currently focused.

**Arguments:**
- `model` _(String)_: the model indicated as blurred (not focused)

## `actions.setPristine(model)`
Returns an action that, when handled by a `formReducer`, changes the `.pristine` state of the field model in the form to `true`, as well as the corresponding `.dirty` state to `false`.

The "pristine" state indicates that the user has not interacted with this field model yet.

**Arguments**
- `model` _(String)_: the model indicated as pristine

**Tips**
- Whenever a field is set to pristine, the entire form is set to:
  - pristine _if_ all other fields are pristine
  - otherwise, dirty.

## `actions.setDirty(model)`
Returns an action that, when handled by a `formReducer`, changes the `.dirty` state of the field model in the form to `true`, as well as the corresponding `.pristine` state to `false`.

The "dirty" state indicates that the model value has been changed.

**Arguments**
- `model` _(String)_: the model indicated as dirty

**Tips**
- Whenever a field is set to dirty, the entire form is set to dirty.

## `actions.setPending(model, [pending])`
Returns an action that, when handled by a `formReducer`, changes the `.pending` state of the field model in the form to `true`. It simultaneously sets the `.submitted` state to `false`.

**Arguments**
- `model` _(String)_: the model indicated as pending
- `pending` _(Boolean)_: whether the model is pending (`true`) or not (`false`).
  - default: `true`

**Tips**
- This action is useful when asynchronously validating or submitting a model. It represents the state between the initial and final state of a field model's validation/submission.

## `actions.setTouched(model)`
Returns an action that, when handled by a `formReducer`, changes the `.touched` state of the field model in the form to `true`. It simultaneously sets the `.untouched` state to `false`.

The "touched" state indicates that this model has been interacted with.

**Arguments**
- `model`: (String) the model indicated as touched

**Tips**
- Setting a `model` to touched also sets the entire form to touched.
- Touched also sets the `model` to blurred.

## `actions.setUntouched(model)`
Returns an action that, when handled by a `formReducer`, changes the `.untouched` state of the field model in the form to `true`. It simultaneously sets the `.touched` state to `true`.

The "untouched" state indicates that this model has not been interacted with yet.

**Arguments**
- `model` _(String)_: the model indicated as untouched

**Tips**
- This action is useful for conditionally displaying error messages based on whether the field has been touched.

## `actions.setSubmitted(model, [submitted])`
Returns an action that, when handled by a `formReducer`, changes the `.submitted` state of the field model in the form to `submitted` (`true` or `false`). It simultaneously sets the `.pending` state to the inverse of `submitted`.

The "submitted" state indicates that this model has been "sent off," or an action has been completed for the model.

**Arguments**
- `model` _(String)_: the model indicated as submitted
- `submitted` _(Boolean)_: whether the model has been submitted (`true`) or not (`false`).
  - default: `true`

**Example**
```js
import { actions } from 'react-redux-form';

// action thunk creator
export default function submitUser(data) {
  return (dispatch) => {
    dispatch(actions.setPending('user', true));
    
    fetch('...', { body: data })
      .then((response) => {
        // handle the response, then...
        dispatch(actions.setSubmitted('user', true));
      });
  }
}
```

**Tips**
- Use the `setPending()` and `setSubmitted()` actions together to update the state of the field model during some async action.

## `actions.setSubmitFailed(model)`
Returns an action that, when handled by a `formReducer`, changes the `.submitFailed` state of the field model in the form to `true`. It simultaneously sets the `.pending` state to `false`, and the `.retouched` state to `false`.

**Arguments**
- `model` _(String)_: the model indicated as having failed a submit

**Tips**

- If the form has not been submitted yet, `.submitFailed = false`
- If submitting (pending), `.submitFailed = false`
- If submit failed, `.submitFailed = true`
- If resubmitting, `.submitFailed = false` again.

## `actions.setInitial(model)`
Returns an action that, when handled by a `formReducer`, changes the state of the field model in the form to its initial state.

Here is the default initial field state:

```js
const initialFieldState = {
  blur: true,
  dirty: false,
  focus: false,
  pending: false,
  pristine: true,
  submitted: false,
  touched: false,
  untouched: true,
  valid: true,
  validating: false,
  viewValue: null,
  validity: {},
  errors: {},
};
```

**Arguments**
- `model` _(String)_: the model to be reset to its initial state

**Tips**
- This action will reset the field state, but will _not_ reset the `model` value in the model reducer. To reset both the field and model, use `actions.reset(model)`.