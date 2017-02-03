# Actions

Redux uses a concept called Actions, which describe state changes to your application. Redux actions are simple JSON objects that implement an `Action`
interface defined by [redux](https://github.com/reactjs/redux).

_app/actions/index.ts_
```typescript
interface Action {
  type: any;
}
```

The `type` property is a value used to uniquely identify your action to your
application. While Redux is un-opinionated about the type of `type`, it is a
common convention to use a string in *lisp-case* (such as `MY_ACTION`),
however you are free to use whatever type and casing style that takes to your
team, as long as it's consistent across the project.

`Action` can be extended to take other properties as well. The most common of these properties is `payload`.

```typescript
interface Action {
  type: any;
  payload?: any;
}
```

The `payload` property provides a way to pass additional data to other parts of Redux, and it's entirely optional.

Here is an example:

```typescript
const loginSendAction: Action = {
  type: 'LOGIN_SEND',
  payload: {
    username: 'katie',
    password: '35c0cd1ecbbb68c75498b83c4e79fe2b'
  }
};
```

> Plain objects are used so that the actions are serializable and can be
replayable into the application state. Even if your actions involve asynchronous
logic, the final dispatched action will remain a plain JSON object.

To simplify action creation, you can create a factory function to take care of
the repeating parts within your application:

_app/store/createAction.ts_
```typescript
import {Action} from '../actions';

export function createAction(type, payload?): Action {
  return { type, payload };
}
```

The resulting creation of the `LOGIN_SEND` action becomes much more succinct and
cleaner:

```typescript
const loginSendAction: Action = createAction('LOGIN_SEND', {
  username: 'katie',
  password: '35c0cd1ecbbb68c75498b83c4e79fe2b'
});
```
