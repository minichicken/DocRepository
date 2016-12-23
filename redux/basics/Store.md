# Store

In the previous sections, we defined the [actions](Actions.md) that represent the facts about “what happened” and the [reducers](Reducers.md) that update the state according to those actions.

이전 세션에서 , 우리는 [액션](Actions.md)은 어떤 것이 발생함에 대한 사실을 나타낸 것이고 [리듀서는](Reducers.md)는 액션에 따라서 변경되는 것에 대한것을 나타냄을 정의했다.


The **Store** is the object that brings them together. The store has the following responsibilities:
**스토어** 는 액션과 리듀서를 가져다주는 객체이다.스토어는 다음과 같은 역할들을 가진다.

* Holds application state; 어플리케이션의 상태를 유지시킨다.
* Allows access to state via [`getState()`](../api/Store.md#getState); `getState()` 메서드를 통해서 상태에 대한 접근을 허용한다.
* Allows state to be updated via [`dispatch(action)`](../api/Store.md#dispatch); `dispatch(action)` 메서드를 통해서 상태에 대한 변화를 허용한다.
* Registers listeners via [`subscribe(listener)`](../api/Store.md#subscribe); `subscribe(listener)` 메서드를 통해서 리스너들을 등록한다.
* Handles unregistering of listeners via the function returned by [`subscribe(listener)`](../api/Store.md#subscribe).
  `subscribe(listener)` 메서드에 의해서 리턴된 함수들을 통해서 리스너의 해지를 다룬다.  

It's important to note that you'll only have a single store in a Redux application. When you want to split your data handling logic, you'll use [reducer composition](Reducers.md#splitting-reducers) instead of many stores.

Redux 어플리케이션에서 하나의 스토어만 가지는 것에 유의하세요. 만약 당신이 다루는 로직의 데이터를 분리한려고 한다면, 당신의 많은 스토어 대신에 reducer 컴포지션을 사용해야 합니다.

It's easy to create a store if you have a reducer. In the [previous section](Reducers.md), we used [`combineReducers()`](../api/combineReducers.md) to combine several reducers into one. We will now import it, and pass it to [`createStore()`](../api/createStore.md).
리듀서를 가지고 스토어를 생성하는 것은 쉽습니다. 이전 리듀서 세션에서 , 우리는 `combineReducers()`메서드를 사용해서 몇개의 리듀서들을 하나로 조합하여 사용했스빈다. 우리는 지금 아주 중요한것을할것이고 , `createStore()` 메서드를 가지고 통과시킬것입니다.

```js

import { createStore } from 'redux'
import todoApp from './reducers'
let store = createStore(todoApp)

```

You may optionally specify the initial state as the second argument to [`createStore()`](../api/createStore.md). This is useful for hydrating the state of the client to match the state of a Redux application running on the server.

```js
let store = createStore(todoApp, window.STATE_FROM_SERVER)
```

## Dispatching Actions

Now that we have created a store, let's verify our program works! Even without any UI, we can already test the update logic.

```js
import { addTodo, toggleTodo, setVisibilityFilter, VisibilityFilters } from './actions'

// Log the initial state
console.log(store.getState())

// Every time the state changes, log it
// Note that subscribe() returns a function for unregistering the listener
let unsubscribe = store.subscribe(() =>
  console.log(store.getState())
)

// Dispatch some actions
store.dispatch(addTodo('Learn about actions'))
store.dispatch(addTodo('Learn about reducers'))
store.dispatch(addTodo('Learn about store'))
store.dispatch(toggleTodo(0))
store.dispatch(toggleTodo(1))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))

// Stop listening to state updates
unsubscribe()
```

You can see how this causes the state held by the store to change:

<img src='http://i.imgur.com/zMMtoMz.png' width='70%'>

We specified the behavior of our app before we even started writing the UI. We won't do this in this tutorial, but at this point you can write tests for your reducers and action creators. You won't need to mock anything because they are just [pure](../introduction/ThreePrinciples.md#changes-are-made-with-pure-functions) functions. Call them, and make assertions on what they return.

## Source Code

#### `index.js`

```js
import { createStore } from 'redux'
import todoApp from './reducers'

let store = createStore(todoApp)
```

## Next Steps

Before creating a UI for our todo app, we will take a detour to see [how the data flows in a Redux application](DataFlow.md).
