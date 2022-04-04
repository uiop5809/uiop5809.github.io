---
layout: single
title: "[React] React.createElement로 컴포넌트 만들기"

categories:
  - React

tags:
  - Web
  - React
  - 라이브러리

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

# createElement

```jsx
React.createElement(
  type, // 태그 이름 문자열 | 리액트 컴포넌트 | React.Fragment
  [props], // 리액트 컴포넌트에 넣어주는 데이터 객체
  [...children] // 자식으로 넣어주는 요소들
);
```

## 1. 태그 이름 문자열 type

```jsx
// <h1>type이 "태그 이름 문자열" 입니다.</h1>
ReactDOM.render(
  React.createElement("h1", null, `type이 "태그 이름 문자열" 입니다.`),
  document.querySelector("#root")
);
```

## 2. 리액트 컴포넌트 type

```jsx
// <Component></Component> => <Component /> => <p>type이 "리액트 컴포넌트" 입니다.</p>
const Component = () => {
  return React.createElement("p", null, `type이 "리액트 컴포넌트" 입니다.`);
};

ReactDOM.render(
  React.createElement(Component, null, null),
  document.querySelector("#root")
);
```

## 3. React.Fragment

```jsx
// 또 하나의 태그 없이 여러개의 요소를 바로 넣을 수 있다.
ReactDOM.render(
  React.createElement(
    React.Fragment,
    null,
    `type이 "React Fragment" 입니다.`,
    `type이 "React Fragment" 입니다.`,
    `type이 "React Fragment" 입니다.`
  ),
  document.querySelector("#root")
);
```

## 4. 복잡한 리액트 엘리먼트 모임

```jsx
// 리액트의 한계 => JSX로 극복
// <div><div><h1>주제</h1><ul><li>React</li><li>Vue</li></ul></div></div>
ReactDOM.render(
  React.createElement(
    "div",
    null,
    React.createElement(
      "div",
      null,
      React.createElement("h1", null, "주제"),
      React.createElement(
        "ul",
        null,
        React.createElement("li", null, "React"),
        React.createElement("li", null, "Vue")
      )
    )
  ),
  document.querySelector("#root")
);
```
