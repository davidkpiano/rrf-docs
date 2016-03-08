# Field Actions

All model and field actions can be imported via `import { actions } from 'react-redux-form'`.

## `actions.focus(model)`
Returns an action that, when handled by a `formReducer`, changes the `.focus` state of the field model in the form to `true`, as well as the corresponding `.blur` state to `false`.

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
Returns an action that, when handled by a `formReducer`, changes the `.blur` state of the field model in the form to `true`, as well as the corresponding `.focus` state to `false`. It also indicates that the field model has been `.touched`, and will set that state to `true` and the `untouched` state to `false`.

The "blur" state indicates that the field model is not focused.

**Arguments:**
- `model` _(String)_: the model indicated as blurred

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

## `actions.setPending(model)`
Returns an action that, when handled by a `formReducer`, changes the `.pending` state of the field model in the form to `true`. It simultaneously sets the `.submitted` state to `false`.

**Arguments**
- `model`: (String) the model indicated as pending

**Tips**
- This action is useful when asynchronously validating or submitting a model. It represents the state between the initial and final state of a field model's validation/submission.


