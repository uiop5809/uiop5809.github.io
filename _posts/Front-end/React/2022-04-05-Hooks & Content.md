---
layout: single
title: "[React] Hooks & Content"

categories:
  - React

tags:
  - Web
  - React

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

# Basic Hooks

- useState
- useEffect
- ~~useContext~~(Context API에서 다룸)

=> function 컴포넌트 사용하는 이유?

- class는 컴포넌트 사이에서 상태와 관련된 로직을 재사용하기 어렵다.
- 복잡한 컴포넌트들은 이해하기 어렵다.
- 사람과 기계를 혼동시킨다.
  - 컴파일 단계에서 코드를 최적화하기 어렵게 만든다.
- this.state는 로직에서 레퍼런스를 공유하기 때문에 문제가 발생할 수 있다.

## useState

state를 대체할 수 있다.

=> useState => count

```jsx
import React from "react";

export default function Example2() {
  const [count, setCount] = React.useState(0); // 배열

  return (
    <div>
      <p>You Clicked {count} times</p>
      <button onClick={click}>Click me</button>
    </div>
  );

  function click() {
    setCount(count + 1);
  }
}
```

=> useState => { count: 0 }

```jsx
import React from "react";

export default function Example3() {
  const [state, setState] = React.useState({ count: 0 }); // 객체

  return (
    <div>
      <p>You Clicked {state.count} times</p>
      <button onClick={click}>Click me</button>
    </div>
  );

  // 1번째 방식
  function click() {
    setState({ count: state.count + 1 });
  }
  // 2번째 방식
  // setState가 사용하는 state는 외부에 의존적이지 않고, 함수의 인자로 들어오는 것이다.
  function click() {
    setState((state) => {
      return {
        count: state.count + 1,
      };
    });
  }
}
```

## useEffect

라이프 사이클 훅을 대체할 수 있다. But 동등한 역할을 한다는 것은 아님

- componentDidMount
- componentDidUpdate
- componentWillUnMount

```jsx
import React from "react";

// useEffect 순차적으로 실행
export default function Example5() {
  const [count, setCount] = React.useState(0); // 배열

  React.useEffect(() => {
    console.log("componentDidMount & componentDidUpdate", count);
  }); // 항상 render가 된 직후에는 이 함수를 실행한다.

  React.useEffect(() => {
    console.log("componentDidMount");
  }, []); // 최초에만 실행된다. []란 무엇에 의존할지

  React.useEffect(() => {
    console.log("componentDidMount & componentDidUpdate by count", count);

    return () => {
      // cleanup
      console.log("cleanup by count", count);
    };
  }, [count]); // 최초와 count가 업데이트 될 때만 실행한다. lifecycle과 100% 일치하지 않음

  React.useEffect(() => {
    console.log("componentDidMount");

    return () => {
      // cleanup
      // componentWillUnmount
    };
  }, []); // 최초에만 실행된다.

  return (
    <div>
      <p>You Clicked {count} times</p>
      <button onClick={click}>Click me</button>
    </div>
  );

  function click() {
    setCount(count + 1);
  }
}
```

# Custom Hooks

window 가로 사이즈 구하는 훅 직접 만들어보기

hooks/useWindowWidth.js

```jsx
import { useState, useEffect } from "react";

export default function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  // componentDidMount
  useEffect(() => {
    const resize = () => {
      setWidth(window.innerWidth);
    };
    window.addEventListener("resize", resize);
    // cleanup
    return () => {
      window.removeEventListener("resize", resize);
    };
  }, []);

  return width;
}
```

App.js

```jsx
import logo from "./logo.svg";
import "./App.css";
import useWindowWidth from "./hooks/useWindowWidth";

function App() {
  const width = useWindowWidth();

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        {width}
      </header>
    </div>
  );
}

export default App;
```

## useHasMounted

hooks/useHasMounted.js

```jsx
import { useState, useEffect } from "react";

export default function useHasMounted() {
  const [hasMounted, setHasMounted] = useState(false);

  useEffect(() => {
    setHasMounted(true);
  }, []);

  return hasMounted;
}
```

App.js

```jsx
import logo from "./logo.svg";
import "./App.css";
import useHasMounted from "./hooks/useHasMounted";

function App() {
  const hasMountedFromHooks = useHasMounted();

  console.log(hasMountedFromHooks);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
      </header>
    </div>
  );
}

export default App;
```

요즘에는 HOC 보다 HOOKS을 더 많이 쓴다.  
그러나 HOC 방식도 어떻게 쓰는지 정확하게 알아놔야 한다.

## withHasMounted(HOC 방식)

hocs/withHasMounted

```jsx
import React from "react";

export default function widthHasMounted(Component) {
  class NewComponent extends React.Component {
    state = {
      hasMounted: false,
    };

    render() {
      const { hasMounted } = this.state;
      return <Component {...this.props} hasMounted={hasMounted} />;
    }

    componentDidMount() {
      this.setState({ hasMounted: true });
    }
  }

  NewComponent.displayName = `withHasMounted(${Component.name})`;

  return NewComponent;
}
```

App.js

```jsx
import logo from "./logo.svg";
import "./App.css";
import withHasMounted from "./hocs/withHasMounted";

function App({ hasMounted }) {
  console.log(hasMounted); // false -> true

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
      </header>
    </div>
  );
}

export default withHasMounted(App);
```

# Additional Hooks

## useReducer

- 다수의 하윗값을 포함하는 복잡한 정적 로직을 만드는 경우
- 다음 state가 이전 state에 의존적인 경우
- Redux를 안다면 쉽게 사용 가능

```jsx
import { useReducer } from "react";

// reducer => state 를 변경하는 로직이 담겨 있는 함수
const reducer = (state, action) => {
  if (action.type === "PLUS") {
    return {
      count: state.count + 1,
    };
  }
  return state;
};

// dispatch => action 객체를 넣어서 실행

// action => 객체이고 필수 프로퍼티로 type을 가진다.

export default function Example6() {
  const [state, dispatch] = useReducer(reducer, { count: 0 }); // 객체

  return (
    <div>
      <p>You Clicked {state.count} times</p>
      <button onClick={click}>Click me</button>
    </div>
  );

  function click() {
    dispatch({ type: "PLUS" });
  }
}
```

## useMemo

```jsx
import { useMemo, useState } from "react";

function sum(persons) {
  console.log("sum...");
  return persons.map((person) => person.age).reduce((l, r) => l + r, 0);
}

export default function Example7() {
  const [value, setValue] = useState("");
  const [persons] = useState([
    { name: "Mark", age: 39 },
    { name: "Hanna", age: 28 },
  ]);

  const count = useMemo(() => {
    return sum(persons);
  }, [persons]); // persons가 변경 됐을 때만 실행

  return (
    <div>
      <input value={value} onChange={change} />
      <p>{count}</p>
    </div>
  );

  function change(e) {
    setValue(e.target.value);
  }
}
```

## useCallback

어떤 함수를 dependency list에 있는 조건에 맞춰서 새로 만들어서 할당해서 사용하게 해주는 것이다.

```jsx
import { useCallback, useMemo, useState } from "react";

function sum(persons) {
  console.log("sum...");
  return persons.map((person) => person.age).reduce((l, r) => l + r, 0);
}

export default function Example7() {
  const [value, setValue] = useState("");
  const [persons] = useState([
    { name: "Mark", age: 39 },
    { name: "Hanna", age: 28 },
  ]);

  const count = useMemo(() => {
    return sum(persons);
  }, [persons]); // persons가 변경 됐을 때만 실행

  const click = useCallback(() => {
    console.log(value);
  }); // [] 함수가 새로 세팅되지 않게 해주고 싶다면

  return (
    <div>
      <input value={value} onChange={change} />
      <p>{count}</p>
      <button onClick={click}>click</button>
    </div>
  );

  function change(e) {
    setValue(e.target.value);
  }
}
```

## useRef

createRef는 항상 reference를 생성해서 render될 때 넣어주는 것이고,  
useRef는 render사이에도 유지해주는 큰 차이가 있다.

```jsx
import { createRef, useRef, useState } from "react";

export default function Example8() {
  const [value, setValue] = useState("");
  const input1Ref = createRef();
  const input2Ref = useRef();

  console.log(input1Ref.current, input2Ref.current);

  return (
    <div>
      <input value={value} onChange={change} />
      <input ref={input1Ref} />
      <input ref={input2Ref} />
    </div>
  );

  function change(e) {
    setValue(e.target.value);
  }
}
```

# React Router Hooks

## useHistory

```jsx
import { useHistory } from "react-router-dom";

export default function LoginButton() {
  const history = useHistory();
  function login() {
    setTimeout(() => {
      history.push("/");
    }, 1000);
  }
  return <button onClick={login}>로그인하기</button>;
}
```

## useParams

```jsx
import { useParams } from "react-router-dom";

export default function LoginButton() {
  const params = useParams();
  const id = params.id;
  console.log(id, typeof id);

  return (
    <div>
      <h2>Profile 페이지입니다.</h2>
      {id && <p> id는 {id} 입니다. </p>}
    </div>
  );
}
```

# 컴포넌트 간 통신

## 하위 컴포넌트 변경

```jsx
import { useState } from "react";

export default function A() {
  const [value, setValue] = useState("아직 안 바뀜");

  return (
    <div>
      <B value={value} />
      <button onClick={click}>E의 값을 바꾸기</button>
    </div>
  );

  function click() {
    setValue("E의 값을 변경");
  }
}

// props 전달
function B({ value }) {
  return (
    <div>
      <p>여긴 B</p>
      <C value={value} />
    </div>
  );
}
// props 전달
function C({ value }) {
  return (
    <div>
      <p>여긴 C</p>
      <D value={value} />
    </div>
  );
}
// props 전달
function D({ value }) {
  return (
    <div>
      <p>여긴 D</p>
      <E value={value} />
    </div>
  );
}

function E({ value }) {
  return (
    <div>
      <p>여긴 E</p>
      <h3>{value}</h3>
    </div>
  );
}
```

## 상위 컴포넌트 변경

```jsx
import { useState } from "react";

export default function A() {
  const [value, setValue] = useState("아직 안 바뀜");

  return (
    <div>
      <p>{value}</p>
      <B setValue={setValue} />
    </div>
  );
}

function B({ setValue }) {
  return (
    <div>
      <p>여긴 B</p>
      <C setValue={setValue} />
    </div>
  );
}

function C({ setValue }) {
  return (
    <div>
      <p>여긴 C</p>
      <D setValue={setValue} />
    </div>
  );
}

function D({ setValue }) {
  return (
    <div>
      <p>여긴 D</p>
      <E setValue={setValue} />
    </div>
  );
}

function E({ setValue }) {
  return (
    <div>
      <p>여긴 E</p>
      <button onClick={click}>클릭</button>
    </div>
  );

  function click() {
    setValue("A 값 변경");
  }
}
```

# Context API

하위 컴포넌트 전체에 데이터를 공유하는 법

- 데이터를 Set 하는 놈
  - 가장 상위 컴포넌트 => 프로바이더
- 데이터를 Get 하는 놈
  - 모든 하위 컴포넌트에서 접근 가능
    - 컨슈머로 하는 방법
    - 클래스 컴포넌트의 this.context로 하는 방법
    - 펑셔널 컴포넌트의 useContext로 하는 방법

## 데이터를 Set하기

1. 일단 컨텍스트를 생성한다.
1. 컨텍스트.프로바이더를 사용한다.
1. value를 사용

가장 상위 컴포넌트 index.js에 프로바이더를 해준다.

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";
import PersonContext from "./contexts/PersonContext.js";

const persons = [
  { id: 0, name: "Mark", age: 39 },
  { id: 1, name: "Hanna", age: 28 },
];

ReactDOM.render(
  <React.StrictMode>
    <PersonContext.Provider value={persons}>
      <App />
    </PersonContext.Provider>
  </React.StrictMode>,
  document.getElementById("root")
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```

contexts/PersonContext

```jsx
import React from "react";

const PersonContext = React.createContext();

export default PersonContext;
```

## 데이터를 Get하기(1) - Consumer

1. 컨텍스트를 가져온다.
1. 컨텍스트.컨슈머를 사용한다.
1. value를 사용

```jsx
import PersonContext from "./../contexts/PersonContext";

export default function Example1() {
  // persons가 PersonContext 프로바이더에 설정된 persons와 같은 것
  return (
    <PersonContext.Consumer>
      {(persons) => (
        <ul>
          {persons.map((person) => (
            <li>{person.name}</li>
          ))}
        </ul>
      )}
    </PersonContext.Consumer>
  );
}
```

## 데이터를 Get하기(2) - class

1. static contextType에 컨텍스트를 설정한다.
1. this.context => value이다.

static contextType은 여러개를 지정할 수 없다.  
그래서 만약에 Persons외에 다른 context에서 데이터를 동시에 가져올 수 없다.

```jsx
import React from "react";
import PersonContext from "./../contexts/PersonContext";

export default class Example2 extends React.Component {
  static contextType = PersonContext;

  render() {
    const persons = this.context;
    return (
      <ul>
        {persons.map((person) => (
          <li>{person.name}</li>
        ))}
      </ul>
    );
  }
}
```

## 데이터를 Get하기(3) - functional

1. useContext로 컨텍스트를 인자로 호출한다.
1. useContext의 리턴이 value이다.

```jsx
import { useContext } from "react";
import PersonContext from "./../contexts/PersonContext";

export default function Example3() {
  const persons = useContext(PersonContext);

  return (
    <ul>
      {persons.map((person) => (
        <li>{person.name}</li>
      ))}
    </ul>
  );
}
```
