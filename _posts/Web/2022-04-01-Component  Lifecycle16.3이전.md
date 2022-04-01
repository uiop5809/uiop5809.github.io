---
layout: single
title: "[React] Component Lifecycle(<v16.3)"

categories:
  - Web

tags:
  - Web
  - React
  - Lifecycle

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

## Component Lifecycle

리액트 컴포넌트는 탄생부터 죽음까지 여러지점에서 개발자가 작업이 가능하도록 메서드를 오버라이딩 할 수 있게 해준다.  
![Declarative](../../images/%EB%94%94%ED%81%B4%EB%A0%88%EB%9F%AC%ED%8B%B0%EB%B8%8C.png)

## Component 생성 및 마운트 (< v16.3)

constructor  
componentWillMount  
render (최초 랜더)  
componentDidMount

```jsx
class App extends React.Component {
  state = {
    age: 39,
  };
  constructor(props) {
    super(props);
    console.log("constructor", props);
  }
  render() {
    console.log("render");
    return (
      <div>
        <h1>
          Hello {this.props.name} - {this.state.age}
        </h1>
      </div>
    );
  }
  componentWillMount() {
    console.log("componentWillMount");
  }
  componentDidMount() {
    console.log("componentDidMount");

    // 타이머
    setInterval(() => {
      // ...state란 위의 state 객체를 복사해주고 state.age + 1한 것을 덮어씌어준다.
      this.setState((state) => ({ ...state, age: state.age + 1 })); // age를 1씩 올리는
    }, 1000);
  }
}
ReactDOM.render(<App name="Yejin" />, document.querySelector("#root"));
```

## Component props, state 변경 (< v16.3)

1. componentWillReceiveProps

   - props를 새로 지정했을 때 바로 호출된다.
   - 여기는 state의 변경에 반응하지 않는다.
   - 여기서 props의 값에 따라 state를 변경해야 한다면,
     - setState를 이용해 state를 변경한다.
     - 그러면 다음 이벤트로 각각 가는 것이 아니라 한 번에 변경된다.

1. shouldComponentUpdate

   - props만 변경되어도
   - state만 변경되어도
   - props & state 둘 다 변경되어도
   - newProps와 newState를 인자로 해서 호출
   - return type이 boolean이다.
     - true면 render
     - false면 render가 호출되지 않는다.
     - 이 함수를 구현하지 않으면, 디폴트는 true

1. componentWillUpdate

   - 컴포넌트가 재랜더링 되기 직전에 불린다.
   - 여기선 setState 같은 것을 쓰면 안 된다.

1. render
1. componentDidUpdate

   - 컴포넌트가 재랜더링을 마치면 불린다.

```jsx
class App extends React.Component {
  state = {
    age: 39,
  };
  constructor(props) {
    super(props);
    console.log("constructor", props);
  }
  render() {
    console.log("render");
    return (
      <div>
        <h1>
          Hello {this.props.name} - {this.state.age}
        </h1>
      </div>
    );
  }
  componentWillMount() {
    console.log("componentWillMount");
  }
  componentDidMount() {
    console.log("componentDidMount");
    // 타이머
    setInterval(() => {
      // ...state란 위의 state 객체를 복사해주고 state.age + 1한 것을 덮어씌어준다.
      this.setState((state) => ({ ...state, age: state.age + 1 })); // age를 1씩 올리는
    }, 1000);
  }

  componentWillReceiveProps(nextProps) {
    console.log("componentWillReceiveProps", nextProps);
  }
  // true 혹은 false 리턴(조건부 처리)
  shouldComponentUpdate(nextProps, nextState) {
    console.log("shouldComponentUpdate", nextProps, nextState);
    return true; // 이어서 render할 준비
    // return false; 다음 단계(render)로 안 넘어감
  }
  // render가 되기 전
  componentWillUpdate(nextProps, nextState) {
    console.log("componentWillUpdate", nextProps, nextState);
  }
  componentDidUpdate(prevProps, prevState) {
    console.log("componentDidUpdate", prevProps, prevState);
  }
}
ReactDOM.render(<App name="Yejin" />, document.querySelector("#root"));
```

## Component 언마운트(<v16.3)

componentWillUnmount

```jsx
class App extends React.Component {
  state = {
    age: 39,
  };
  interval = null; // interval 정의
  constructor(props) {
    super(props);
    console.log("constructor", props);
  }
  render() {
    console.log("render");
    return (
      <div>
        <h1>
          Hello {this.props.name} - {this.state.age}
        </h1>
      </div>
    );
  }
  componentWillMount() {
    console.log("componentWillMount");
  }
  componentDidMount() {
    console.log("componentDidMount");
    // 타이머
    this.interval = setInterval(() => {
      // ...state란 위의 state 객체를 복사해주고 state.age + 1한 것을 덮어씌어준다.
      this.setState((state) => ({ ...state, age: state.age + 1 })); // age를 1씩 올리는
    }, 1000);
  }

  componentWillReceiveProps(nextProps) {
    console.log("componentWillReceiveProps", nextProps);
  }
  // true 혹은 false 리턴(조건부 처리)
  shouldComponentUpdate(nextProps, nextState) {
    console.log("shouldComponentUpdate", nextProps, nextState);
    return true; // 이어서 render할 준비
    // return false; 다음 단계(render)로 안 넘어감
  }
  // render가 되기 전
  componentWillUpdate(nextProps, nextState) {
    console.log("componentWillUpdate", nextProps, nextState);
  }
  componentDidUpdate(prevProps, prevState) {
    console.log("componentDidUpdate", prevProps, prevState);
  }

  // setInterval 해제해줘야됨
  // 컴포넌트가 사라지면 실행될 것
  componentWillUnmount() {
    clearInterval(this.interval);
  }
}
ReactDOM.render(<App name="Yejin" />, document.querySelector("#root"));
```
