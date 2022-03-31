---
layout: single
title: "[React] Props와 State, Render함수"

categories:
  - Web

tags:
  - Web
  - React
  - 라이브러리
  - props
  - state

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

## Props와 State

![props와 state](../../images/props%EC%99%80%20state.png)

## Render 함수

Props와 State를 바탕으로 컴포넌트를 그린다.  
그리고 Props와 State가 변경되면, 컴포넌트를 다시 그린다.  
컴포넌트를 그리는 방법을 기술하는 함수가 랜더 함수이다.

![render](../../images/render%ED%95%A8%EC%88%98.png)

## function 컴포넌트(props) {return JSX;}

```jsx
// props에는 {message: "안녕하세요"} 객체가 들어옴
function Component(props) {
  return (
    <div>
      <h1>{props.message} 이것은 함수로 만든 컴포넌트입니다.</h1>
    </div>
  );
}
ReactDOM.render(
  <Component message="안녕하세요" />,
  document.querySelector("#root")
);
```

=> 기본값 설정하는 법

```jsx
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다.</h1>
      </div>
    );
  }
}

// 1번째 방법
Component.defaultProps = {
  message: "기본값",
};

ReactDOM.render(<Component />, document.querySelector("#root"));
```

## class 컴포넌트 extends React.Component

```jsx
class Component extends React.Component {
  // state는 항상 객체여야함
  // 1번째 방법
  state = {
    count: 0,
  };
  // 2번째 방법
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다.</h1>
        <p>{this.state.count}</p>
      </div>
    );
  }

  componentDidMount() {
    setTimeout(() => {
      // 1번째 방법 (객체를 통째로 새로 만들어서 넣는 방식)
      this.setState({
        count: this.state.count + 1,
      });
      // 2번째 방법 (이전 값을 이용할 수 있음)
      this.setState((previousState) => {
        const newState = { count: previousState.count + 1 };
        return newState;
      });
    }, 1000);
  }
}

ReactDOM.render(
  <Component message="안녕하세요" />,
  document.querySelector("#root")
);
```

=> 기본값 설정하는 법

```jsx
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클래스로 만든 컴포넌트입니다.</h1>
      </div>
    );
  }
  // 1번째 방법(class 컴포넌트만 가능)
  static defaultProps = {
    message: "기본값",
  };
}
// 2번째 방법
Component.defaultProps = {
  message: "기본값",
};

ReactDOM.render(<Component />, document.querySelector("#root"));
```
