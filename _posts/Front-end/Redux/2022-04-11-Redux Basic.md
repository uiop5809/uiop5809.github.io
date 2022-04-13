---
layout: single
title: "[Redux] Redux Basic"

categories:
  - Redux

tags:
  - Web
  - React
  - Redux

author_profile: true # 왼쪽부분 프로필을 띄울건지

header:
  overlay_image: https://images.unsplash.com/photo-1501785888041-af3ef285b470?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1350&q=80
  overlay_filter: 0.5 # 투명도

# table of contents
toc: true # 오른쪽 부분에 목차를 자동 생성해준다.
toc_label: " table of content" # toc 이름 설정
toc_icon: "bars" # 아이콘 설정
toc_sticky: true # 마우스 스크롤과 함께 내려갈 것인지 설정
---

# Redux 개요

component - Communication  
Context를 사용할 경우에 Context가 가지고 있는 전역 데이터를 효과적으로 관리할 것에 대한 라이브러리가 Redux이다.

![redux](../../../images/redux/redux.png)

보라색 공이 store안에 상태를 변경하려는 것이고  
하늘색은 상태가 변했으니 다시 render하는 것이다.

1. 단일 store를 만드는 법
1. 리액트에서 store 사용하는 법

- 단일 스토어다.
- [만들기] 단일 스토어 사용 준비하기
  - import redux
  - 액션을 정의하고
  - 액션을 사용하는 리듀서를 만들고,
  - 리듀서들을 합친다.
  - 최종 합쳐진 리듀서를 인자로, 단일 스토어를 만든다.
- [사용하기] 준비한 스토어를 리액트 컴포넌트에서 사용하기
  - import react-redux
  - connect 함수를 이용해서 컴포넌트에 연결

# Action 액션

## 리덕스의 액션

- 액션은 사실 객체(object)이다.
- 두 가지 형태의 액션이 있다.
  - {type: "TEST"} // payload 없는 액션
  - {type: "TEST", params: "hello"} // payload 있는 액션
- type만이 필수 프로퍼티이며, type은 문자열이다.

## 리덕스의 액션 생성자

    function 액션생성자(...args) { return 액션; }

- 액션을 생성하는 함수를 "액션 생성자(Action Creator)"라고 한다.
- 함수를 통해 액션을 생성해서, 액션 객체를 리턴해준다.
- createTest("hello"); // {type: "TEST", params: "hello" } 리턴

## 리덕스의 액션 하는 일

- 액션 생성자를 통해 액션을 만들어낸다.
- 만들어낸 액션 객체를 리덕스 스토어에 보낸다.
- 리덕스 스토어가 액션 객체를 받으면 스토어의 상태 값이 변경된다.
- 변경된 상태 값에 의해 상태를 이용하고 있는 컴포넌트가 변경된다.
- 액션은 스토어에 보내는 일종의 인풋이라 생각할 수 있다.

## 액션을 준비하기 위해서는?

- 액션의 타입을 정의하여 변수로 빼는 단계
  - 강제는 아니다. (안해도 된다.)
  - 그냥 타입을 문자열로 넣기에는 실수를 유발할 가능성이 크다.
  - 미리 정의한 변수를 사용하면, 스펠링에 주의를 덜 기울여도 된다.
- 액션 객체를 만들어내는 함수를 만드는 단계
  - 하나의 액션 객체를 만들기 위해 하나의 함수를 만들어낸다.
  - 액션의 타입은 미리 정의한 타입 변수로부터 가져와서 사용한다.

액션의 타입을 정의하고, 액션 생성자를 하나 만들었다.

redux/actions.js

```js
const ADD_TODO = "ADD_TODO";

// 액션 생성 함수
function addTodo(todo) {
  return {
    type: ADD_TODO,
    todo, // todo: todo,
  };
}
```

# Reducers - 리듀서

- 액션을 주면, 그 액션이 적용되어 달라진(안 달라질 수도 있음) 결과를 만들어 준다.
- 그냥 함수이다.

  - Pure Function
  - Immutable

    - 리듀서를 통해 상태가 달라졌음을 리덕스가 인지하는 방식이다.

    function 리듀서(previousState, action) {
    return newState;
    }

- 액션을 받아서 state를 리턴하는 구조
- 인자로 들어오는 previousState와 리턴되는 newState는 다른 참조를 가지도록 해야한다.

redux/actions.js

```js
export const ADD_TODO = "ADD_TODO"; // 문자열보단 변수

// 액션 생성자 함수
function addTodo(todo) {
  return {
    type: ADD_TODO,
    todo, // todo: todo,
  };
}
```

redux/reducers.js

```js
import { ADD_TODO } from "./actions";

// state
// ["코딩", "점심 먹기"];
const initialState = [];

function todoApp(previousState = initialState, action) {
  // 초기값을 설정해주는 부분
  // if (previousState === undefined) {
  //   return [];
  // }
  if (action.type === ADD_TODO) {
    return [...previousState, action.todo]; // 새로운 객체
  }

  // previousState.push(""); // 객체는 변경되지만, 레퍼런스는 변경 안됨

  return previousState; // 아무 변화 없음
}
```

# createStore

redux로 부터 import

## 스토어를 만드는 함수

    const store = createStore(리듀서);

```js
createStore<S> (
  reducer: Reducer<S>,
  preloadedState: S,
  enhancer?: StoreEnhancer<S>
): Store<S>;
```

redux/store.js

```js
import { createStore } from "redux";
import { todoApp } from "./reducers";

const store = createStore(todoApp); // 스토어 만드는 함수

export default store;
```

index.js

```js
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import store from "./redux/store";
import { addTodo } from "./redux/actions.js";

// store에 변경사항이 생기는 것을 구독한다.
// store에 상태가 변경되면 호출됨
const unsubscribe = store.subscribe(() => {
  console.log(store.getState());
});

// action객체를 넣어서 실행
store.dispatch(addTodo("coding"));
store.dispatch(addTodo("read book"));
store.dispatch(addTodo("eat"));
unsubscribe();
store.dispatch(addTodo("coding"));
store.dispatch(addTodo("read book"));
store.dispatch(addTodo("eat"));

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById("root")
);

reportWebVitals();
```

## store 정리

- store.getState();
- store.dispatch(액션); store.dispatch(액션생성자());
- cosnt unsubscribe = store.subscribe(()=>{});
  - 리턴이 unsubscribe 라는 점!
  - unsubscribe(); 하면 제거
- store.replaceReducer(다른 리듀서);

# combineReducers

// [{text: "코딩", done: false}, {text: "점심 먹기", done: false}]
// {todos: [{text: "코딩", done: false}, {text: "점심 먹기", done: false}], filter: "ALL"}

# Redux를 React에 연결
