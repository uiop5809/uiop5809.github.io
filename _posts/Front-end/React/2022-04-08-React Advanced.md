---
layout: single
title: "[React] React Advanced"

categories:
  - React

tags:
  - Web
  - React
  - Advanced

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

# Optimizing Performance

필요할 때만 render하며 불필요한 render 줄이기

=> Reconciliation

- render 전후의 일치 여부를 판단하는 규칙

```jsx
import logo from "./logo.svg";
import "./App.css";
import React from "react";

class Foo extends React.Component {
  componentDidMount() {
    console.log("Foo componentDidMount");
  }
  componentWillUnmount() {
    console.log("Foo componentWillUnmount");
  }
  render() {
    return <p>Foo</p>;
  }
}

class App extends React.Component {
  state = {
    count: 0,
  };

  componentDidMount() {
    setInterval(() => {
      this.setState({ count: this.state.count + 1 });
    }, 1000);
  }

  // 상위 타입이 다르므로 Unmount했다가 mount했다가 한다.
  render() {
    if (this.state.count % 2 === 0) {
      return (
        <div>
          <Foo />
        </div>
      );
    }
    return (
      <span>
        <Foo />
      </span>
    );
  }
}

export default App;
```

- 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.

```jsx
import logo from "./logo.svg";
import "./App.css";
import React from "react";

class App extends React.Component {
  state = {
    count: 0,
  };

  componentDidMount() {
    setInterval(() => {
      this.setState({ count: this.state.count + 1 });
    }, 1000);
  }

  // 같은 dom 엘리먼트인데 다른 속성을 갖게 되면
  // 달라진 속성 값만 바뀌면서 다시 표현된다.
  render() {
    if (this.state.count % 2 === 0) {
      return (
        <div className={% raw %}{{ color: "red", fontWeight: "bold" }}{% endraw %} title="stuff" />
      );
    }
    return (
      <div className={% raw %}{{ color: "green", fontWeight: "bold" }}{% endraw %} title="stuff" />
    );
  }
}

export default App;
```

```jsx
import logo from "./logo.svg";
import "./App.css";
import React from "react";

class Foo extends React.Component {
  componentDidMount() {
    console.log("Foo componentDidMount");
  }
  componentWillUnmount() {
    console.log("Foo componentWillUnmount");
  }

  static getDerivedStateFromProps(nextProps, prevProps) {
    console.log("Foo getDerivedStateFromProps", nextProps, prevProps);
    return {};
  }

  render() {
    console.log("Foo render");
    return <p>Foo</p>;
  }
}

class App extends React.Component {
  state = {
    count: 0,
  };

  componentDidMount() {
    setInterval(() => {
      this.setState({ count: this.state.count + 1 });
    }, 1000);
  }

  // 다른 props면 컴포넌트가 업데이트 된다.
  render() {
    if (this.state.count % 2 === 0) {
      return <Foo name="Mark" />;
    }
    return <Foo name="Hanna" />;
  }
}

export default App;
```

- key prop을 통해, 여러 랜더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해줄 수 있다.

```jsx
import logo from "./logo.svg";
import "./App.css";
import React from "react";

class Foo extends React.Component {
  componentDidMount() {
    console.log("Foo componentDidMount", this.props.children);
  }
  componentWillUnmount() {
    console.log("Foo componentWillUnmount");
  }

  static getDerivedStateFromProps(nextProps, prevProps) {
    console.log("Foo getDerivedStateFromProps", nextProps, prevProps);
    return {};
  }

  render() {
    console.log("Foo render", this.props.children);
    return <p>Foo</p>;
  }
}

class App extends React.Component {
  state = {
    count: 0,
  };

  componentDidMount() {
    setInterval(() => {
      this.setState({ count: this.state.count + 1 });
    }, 1000);
  }

  render() {
    if (this.state.count % 2 === 0) {
      return (
        <ul>
          <Foo key="2">second</Foo>
          <Foo key="3">third</Foo>
        </ul>
      );
    }
    return (
      <ul>
        <Foo key="1">first</Foo>
        <Foo key="2">second</Foo>
        <Foo key="3">third</Foo>
      </ul>
    );
  }
}

export default App;
```

class 버전

```jsx
import logo from "./logo.svg";
import "./App.css";
import React from "react";

// class Person extends React.PureComponent {
//   render() {
//     console.log("Person render");
//     const { name, age } = this.props;
//     return (
//       <div>
//         {name} / {age}
//       </div>
//     );
//   }
// }

class Person extends React.Component {
  shouldComponentUpdate(previousProps) {
    for (const key in this.props) {
      if (previousProps[key] !== this.props[key]) {
        return true;
      }
    }
    return false; // 완전 같음, 불필요한 render 하지 않음
  }

  render() {
    console.log("Person render");
    const { name, age } = this.props;
    return (
      <div>
        {name} / {age}
      </div>
    );
  }
}

class App extends React.Component {
  state = {
    text: " ",
    persons: [
      { id: 1, name: "Mark", age: 39 },
      { id: 2, name: "Yejin", age: 22 },
    ],
  };

  render() {
    const { text, persons } = this.state;

    return (
      <div>
        <input type="text" value={text} onChange={this.change} />
        <ul>
          {persons.map((person) => {
            return (
              <Person
                {...person}
                key={person.id}
                onClick={this.toPersonClick}
              />
            );
          })}
        </ul>
      </div>
    );
  }

  change = (e) => {
    this.setState({ ...this.state, text: e.target.value });
  };

  toPersonClick = () => {};
}

export default App;
```

function 버전

```jsx
import logo from "./logo.svg";
import "./App.css";
import React from "react";

// props가 같을 때, 다시 render안 하게 함
const Person = React.memo(({ name, age }) => {
  console.log("Person render");
  return (
    <div>
      {name} / {age}
    </div>
  );
});

function App() {
  const [state, setState] = React.useState({
    text: " ",
    persons: [
      { id: 1, name: "Mark", age: 39 },
      { id: 2, name: "Yejin", age: 22 },
    ],
  });
  const toPersonClick = React.useCallback(() => {}, []);

  const { text, persons } = state;

  return (
    <div>
      <input type="text" value={text} onChange={change} />
      <ul>
        {persons.map((person) => {
          return <Person {...person} key={person.id} onClick={toPersonClick} />;
        })}
      </ul>
    </div>
  );

  function change(e) {
    setState({ ...state, text: e.target.value });
  }
}

export default App;
```

# React.createPortal

Portal은 부모 컴포넌트의 DOM 계층 구조 바깥에 있는 DOM 노드로 자식은 렌더링하는 최고의 방법을 제공한다.

public/index.html

```jsx
<div id="root"></div>
<div id="modal"></div> // 추가
```

index.css

```jsx
#modal {
  position: absolute;
  top: 0;
  left: 0;
}
```

components/Modal.jsx

```jsx
import ReactDOM from "react-dom";

// children이 createPortal로 생성돼서 id가 modal인 element에 render
const Modal = ({ children }) =>
  ReactDOM.createPortal(children, document.querySelector("#modal"));

export default Modal;
```

App.js

```jsx
import logo from "./logo.svg";
import "./App.css";
import React, { useState } from "react";
import Modal from "./components/Modal";

function App() {
  const [visible, setVisible] = useState(false);

  const open = () => {
    setVisible(true);
  };
  const close = () => {
    setVisible(false);
  };

  return (
    <div>
      <button onClick={open}>open</button>
      {visible && (
        <Modal
          style={% raw %}{{
            width: "100vw",
            height: "100vh",
            background: "rgba(0,0,0,0.5)",
          }}{% endraw %}
        >
          <div onClick={close}>Hello</div>
        </Modal>
      )}
    </div>
  );
}

export default App;
```

# React.forwardRef

하위 컴포넌트에 있는 레퍼런스를 상위 컴포넌트에서 이용하는 것이다.

특정 input에 대해서 컴포넌트를 만들때 혹은 다른 이유에서든 reference를 나의 하위 컴포넌트 어디에선가 지정을 해주고 싶다면 forwardRef로 전달을 해줄 수 있다.

App.js

```jsx
import logo from "./logo.svg";
import "./App.css";
import React, { useRef, useState } from "react";
import MyInput from "./components/MyInput";

function App() {
  const myInputRef = useRef();
  // MyInput.jsx의 input ref를 가져오는 것

  const click = () => {
    console.log(myInputRef.current.value);
  };

  return (
    <div>
      <MyInput ref={myInputRef} />
      <button onClick={click}>콘솔에 출력</button>
    </div>
  );
}

export default App;
```

MyInput.jsx

```jsx
import React from "react";

export default React.forwardRef(function MyInput(props, ref) {
  return (
    <div>
      <p>MyInput</p>
      <input ref={ref} />
    </div>
  );
});
```
