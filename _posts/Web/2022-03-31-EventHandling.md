---
layout: single
title: "[React] Event Handling"

categories:
  - Web

tags:
  - Web
  - React
  - Event handling

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

## Event Handling

HTML DOM에 클릭하면 이벤트가 발생하고, 발생하면 그에 맞는 변경이 일어나도록 해야한다.  
JSX에 이벤트를 설정할 수 있다.

- camelCase로만 사용할 수 있다.
  - onClick, onMouseEnter
- 이벤트에 연결된 자바스크립트 코드는 함수이다.
  - 이벤트={함수} 와 같이 쓴다.
- 실제 DOM 요소들에만 사용 가능하다.
  - 리액트 컴포넌트에 사용하면, 그냥 props로 전달한다.

## 종류

onClick, onMouseEnter

## function Component

```jsx
function Component() {
  return (
    <div>
      <button
        onClick={() => {
          console.log("clicked");
        }}
      >
        클릭
      </button>
    </div>
  );
}
ReactDOM.render(<Component />, document.querySelector("#root"));
```

## class Component

```jsx
class Component extends React.Component {
  state = {
    count: 0,
  };
  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button
          onClick={() => {
            console.log("clicked");
            // ...state란 위의 state 객체를 복사해주고 state.count + 1한 것을 덮어씌어준다.
            this.setState((state) => ({ ...state, count: state.count + 1 }));
          }}
        >
          클릭
        </button>
      </div>
    );
  }
}
ReactDOM.render(<Component />, document.querySelector("#root"));
```

=> 함수를 별도의 메서드로 분리

- this에 바인딩해주는 방식

  1.메서드가 JSX를 통해 각 DOM에 이벤트로 등록되는 과정에서, 컴포넌트 인스턴스의 관계가 끊기게 된다. 그렇기에 클릭시 this는 undefind가 될 것이며, onClick 이벤트 핸들러 내에서도 this를 인식할 수 없다. 그래서 아래처럼 처음 constructor() 메서드에서 이벤트 핸들러에 현재 컴포넌트인 this를 바인딩 해준다.

  ```jsx
  class Component extends React.Component {
    state = {
      count: 0,
    };
    constructor(props) {
      super(props);
      this.click = this.click.bind(this);
    }
    render() {
      return (
        <div>
          <p>{this.state.count}</p>
          <button onClick={this.click}>클릭</button>
        </div>
      );
    }
    click() {
      console.log("clicked");
      this.setState((state) => ({ ...state, count: state.count + 1 }));
    }
  }
  ReactDOM.render(<Component />, document.querySelector("#root"));
  ```

  2.이벤트 핸들러를 Arrow Function 형태로 정의한다. ES6의 Arrow Function 문법이 this bind를 지원하기 때문에 별도의 바인딩이 필요없다.

  ```jsx
  class Component extends React.Component {
    state = {
      count: 0,
    };
    render() {
      return (
        <div>
          <p>{this.state.count}</p>
          <button onClick={this.click}>클릭</button>
        </div>
      );
    }
    click = () => {
      console.log("clicked");
      this.setState((state) => ({ ...state, count: state.count + 1 }));
    };
  }
  ReactDOM.render(<Component />, document.querySelector("#root"));
  ```

## 이벤트 기본 동작 방지

DOM의 경우에는 이벤트 핸들러가 false를 반환하면 기본동작이 발생하지 않으나, React는 무조건적으로 발생한다. 이를 방지하기 위해, React에서는 **preventDefault()**라는 메서드를 활용할 수 있다.

```jsx
const handleClick = (e) => {
  e.preventDefault();
};

<a href="#" onClick={handleClick}>
  Click Me!
</a>;
```
