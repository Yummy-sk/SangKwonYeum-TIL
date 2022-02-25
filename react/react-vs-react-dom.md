# React vs React-DOM

## React vs React-DOM

우리는 지금 React의 시대에 살고 있다고 해도 과언이 아닐 정도로 많은 개발자가 프론트앤드 스택으로, React를 선택하며, React로 개발하고 있습니다.

\
React로 개발해 보신 경험이 있다면, 한 번쯤 맞닥뜨렸던 React-Dom..\


대표적으로 `index.jsx` 를 보면, React와 React-DOM을 `import` 하는 것을 볼 수 있습니다.\


```jsx
import React from 'react';
import ReactDOM from 'react-dom';

import './index.css';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```



**저는 이 둘의 차이점과 역할은 무엇이며 React 팀은 왜 위와 같이 React-DOM을 나눠 두었을까에 대해 알아보려 합니다.**

### React와 React-DOM

#### ⚛ React 란..?

> React란, User Interface를 만들기 위한 자바스크립트 라이브러리 입니다.

개인적으로 이 글을 쓰기 전까지는 리액트는 웹 프론트앤드를 위한 라이브러리라고 생각했습니다.



하지만, 위에서도 언급되었다시피 \_**React는 User Interface 컴포넌트 형태로 구성하는 라이브러리**\_일 뿐이며 웹 또는 브라우저를 위한 라이브러리는 아닙니다.



_**React는 컴포넌트와 props, state, context를 관리하며, 이들의 변경 사항을 파악하고 변경 사항 snap shot을 React-DOM에 전달합니다.**_

#### 🌲 React-DOM

> React-DOM은 브라우저의 DOM에서 React를 사용하기 위한 라이브러리입니다.

\_**React-DOM은 웹 인터페이스로 웹과 직접적인 연관이 있으며 실제 HTML 요소를 화면에 불러오는 역할**\_을 합니다.\


따라서, React-DOM은 \_**React로 부터 받은 변경 사항 snap shot과 실제 브라우저 DOM을 비교**\_하며 \_**차이점을 확인 후, 실제 DOM을 조작**\_합니다.



즉, 실질적인 브라우저 DOM의 조작은 React-DOM이 하게 되며 \_**리액트 컴포넌트의 이전 state와 현재 state를 비교한 뒤, 차이점이 있을 때 업데이트**\_됩니다.

![Group 1.png](https://i.imgur.com/rBZjnxT.png)

***

### React와 React-DOM이 나뉘게 된 이유

React 팀이 React와 React-DOM을 각기 다른 라이브러리로 나누기로한 이유는 무엇일까요??\


그 이유는 우리가 위에서도 알아보았습니다.\


React-DOM은 React의 아이디어를 웹 브라우저와 바인딩하는 도구이기 때문이며 이상적으로, _**리액트는 브라우저 또는 웹과 아무런 관련이 없습니다.**_\


이것이, \_**React-Native와 React-Three가 개발되고 사용되고 있는 이유**\_입니다.



위와 같은 라이브러리는 React-DOM을 사용하진 않지만 실제로는 React의 아이디어를 사용합니다.

***

### 참고

[React vs React-DOM](https://medium.com/programming-sage/react-vs-react-dom-a0ed3aea9745)
