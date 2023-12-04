# React

## Basic concepts

### State and Props

| State                                                                   | Props                                     |
| ----------------------------------------------------------------------- | ----------------------------------------- |
| - store data within a component, rerender component everytime it change | - pass data or handler to child component |
| - mutable                                                               | - immutable                               |

### Class Component and Functional Component

| Functional Component                                            | Class Component                                    |
| --------------------------------------------------------------- | -------------------------------------------------- |
| - a Jspure function that accepts props and return React element | - a class extend React Component                   |
| - stateless component                                           | - statefull component                              |
| - need React Hooks to manage state and lifecycle                | - internal methods (render, componentDidMount,...) |

## State management

### Redux

> A predictable state container to keep the consistency state across application. App state will be kept in store for any component access any state.
>
> Not directly modify the state, instead, dispatch actions that intend to change the state, and then separately, observe the resulting state changes.
>
> Redux doesn't know about async logic, it only know (dispatch actions, update the state by calling reducer, and notify the UI), any asynchronicity has to happen outside the store. `That's why Redux middleware were designed to enable writing logic that has side effects.`

### Redux Saga / Redux Thunk

`Redux Thunk` is a middleware that lets you call action creators that return a function instead of an action object. That function receives the store’s dispatch method, which is then used to dispatch regular synchronous actions inside the body of the function once the asynchronous operations have completed.

```js
export const asyncApiCall = values => {
  return dispatch => {
    return axios.get(url).then(response => {
      dispatch(successHandle(response));
    });
  };
};

export const successHandle = response => {
  return {
    // return action object
    type: SUCCESS,
    data: response.data,
  };
};
```

`Redux Saga` provide helper effect use an ES6 feature called `Generators`:

```js
const fetchPosts = payload => {
  return fetch(url)
    .then(response => response.json())
    .catch(err => {
      throw err;
    });
};

function* sagaWatcher() {
  yield takeLatest(FETCH_POSTS, fetchPostWorker);
}

function* fetchPostWorker({ payload }) {
  try {
    const data = yield call(fetchPosts, payload);
    yield put({
      type: FETCH_POSTS_SUCCESS,
      data,
    });
  } catch (err) {
    yield put({
      type: FETCH_POSTS_FAIL,
    });
  }
}
```

- takeEvery(): start a task on each action dispatched to the Store that matches pattern.
- takeLastest(): if started previously tasks is still running, they will be canceled
- put(): dispatch action.
- call(): call function

### Redux Persist

> A library that enables persisting Redux state across page reloads or app restarts
