## 클래스 형 과 함수형 컴포넌트의 오해

1.  **클래스는 함수형 컴포넌트 보다 더 많은 기능을 제공한다?**

    [Hook](https://ko.reactjs.org/docs/hooks-intro.html)이 React 버전 16.8 부터 추가된 이후로 부터는 그렇다 볼 수 없습니다.

2.  **둘 중 하나가 성능면에서 조금 더 유리하다?**

    이에 대한 답을 얻기 위해 여러 벤티마킹 실험들이 이루어졌다고 합니다. 하지만 대부분의 결과들이 [신뢰할 수 없는 것](https://medium.com/@dan_abramov/this-benchmark-is-indeed-flawed-c3d6b5b6f97f)으로 밝혀졌습니다.

    사실 성능은 함수냐 클래스냐 보다는 무슨 동작을 하는 코드냐에 더 큰 영향을 받습니다.

## 함수형 컴포넌트는 렌더링된 값들을 고정시킨다.

### 😼 함수형 컴포넌트

```jsx
function ProfilePage(props) {
    const showMessage = () => {
        alert('Followed ' + props.user);
    };

    const handleClick = () => {
        setTimeout(showMessage, 3000);
    };

    return <button onClick={handleClick}>Follow</button>;
}
```

### 😺 클래스형 컴포넌트

```jsx
class ProfilePage extends React.Component {
    showMessage = () => {
        alert('Followed ' + this.props.user);
    };

    handleClick = () => {
        setTimeout(this.showMessage, 3000);
    };

    render() {
        return <button onClick={this.handleClick}>Follow</button>;
    }
}
```

위 코드는 `setTimeout` 을 사용해서 버튼을 누르면 네트워크 요청을 통해 `props.user` 가 `Dan` 이라면 알림창에 `Followed Dan` 을 출력하는 코드입니다.

일반적으로 함수형 컴포넌트를 클래스형 컴포넌트로 바꾸거나 또는 그 반대의 과정을 거친다면 위와 같이 변환하는 과정을 거칠 것 입니다.

**하지만, 두 코드는 미묘하게 다르게 동작합니다.** → [Live demo](https://codesandbox.io/s/pjqnl16lm7)

각각의 컴포넌트는 각자의 버튼을 가지고 있고 알림을 띄우는 함수를 가지고 있습니다.

### 👽 어떤게 다르게 동작할까?

**이제 두 버튼에 각각 대해 다음과 같은 순서로 조작해봅시다.**

1.  Follow 버튼을 누르고
2.  3초가 지나기 전에 선택된 프로필을 바꾸자
3.  알림창의 글을 읽어봅사다.

-   함수형 컴포넌트

    ![화면-기록-2021-08-25-오후-5.28.00.gif](https://i.imgur.com/kiT3w5L.gif)

    중간에 `Sophie` 로 바꿨는데, `Dan` 이 그대로 나온다.

-   클래스형 컴포넌트

    ![화면-기록-2021-08-25-오후-5.28.47.gif](https://i.imgur.com/8mqvr9M.gif)

    중간에 `Sophie` 로 바꿨고, 바꾼대로 나온다.

같은 컴포넌트라 생각했으나, 서로 출력하는 결과가 다릅니다..

### 🙈 왜 다르게 동작할까?

일단 클래스는 왜 그런식으로 동작하는지 알아봅시다.

```jsx
class ProfilePage extends React.Component {
  showMessage = () => {
    alert('Followed ' + this.props.user);
  };

```

클래스의 `showMessage` 는 `this.props.user` 로 부터 값을 불러옵니다. `Props` 는 리액트에서 불변의 값입니다. **하지만, `this` 는 변경이 가능하며 조작할 수 있습니다.**

즉, 변경 가능한 `this` 로 인해 시간이 지남에 따라 `this` 를 리액트가 변경하기 때문에 `render` 나 라이프사이클 메서드를 호출할 때 업데이트된 값을 읽어올 수 있는 것 입니다.

따라서, `showMessage` 의 요청이 진행되고 있는 상황에서 클래스 컴포넌트가 다시 랜더링 된다면 `this.props` 또한 바뀌게 되는 것 입니다. → **"새로운" `props` 의 `user` 를 읽는 것.**

위 사실로, UI가 현재 애플리케이션 상태를 보여주는 함수라면, **이벤트 핸들러 또한 시작적으로 컴포넌트와 같이 랜더링 결과의 한 부분이 되는 것 입니다. → 랜더링 시, 같이 결과를 리턴하는 종속관계**

하지만 `this.props` 를 읽는 콜백을 가진 `timeout` 이 사용되면서, 위의 종속관계가 깨져버렸으며, 올바른 `props` 또한 잃게 되었습니다. → `this` 로 부터 값을 읽어오는 동작의 결과

### 🤭 만약 위 상황에서 함수형 컴포넌트가 없다 가정해봅시다.

이를 위해 `render` 와 올바른 `props` , 그리고 이를 읽는 `showMessage` 사이의 관계를 다시 잡아주어야 합니다.

1.  `**this.props` 를 조금 더 일찍 부르고 `timeout` 함수에게는 미리 저장한 값을 전달하는 방법\*\*

    ```jsx
    class ProfilePage extends React.Component {
        showMessage = user => {
            alert('Followed ' + user);
        };

        handleClick = () => {
            const { user } = this.props;
            setTimeout(() => this.showMessage(user), 3000);
        };

        render() {
            return <button onClick={this.handleClick}>Follow</button>;
        }
    }
    ```

    그러나 이 방법에도 문제가 있습니다. 우리가 사용하는 상태 값이나 `props` 가 많아지면 많아질 수 록, **일일히 패러미터에 넣어줘야하기 때문에, 코드의 복잡도가 이에 비례하여 증가할 것 입니다..**

2.  **클로저를 사용하는 방법**

    리엑트에서 `props` 와 `state` 는 불변( **immutable** )값입니다. 이에 따라 특정 `render` 에서 `props`

    와 `state` 를 클로저로 감싸주는 방법입니다.

    ```jsx
    class ProfilePage extends React.Component {
        render() {
            // props의 값을 고정!
            const props = this.props;

            // Note: 여긴 *render 안에* 존재하는 곳이다!
            // 클래스의 메서드가 아닌 render의 메서드
            const showMessage = () => {
                alert('Followed ' + props.user);
            };

            const handleClick = () => {
                setTimeout(showMessage, 3000);
            };

            return <button onClick={handleClick}>Follow</button>;
        }
    }
    ```

    즉, 여기에서의 컨셉은 `render` 함수가 호출 될때, 값을 기억해둔다는 것 입니다. **그러기 때문에 클로저 안에 있는 코드는 `render` 당시의 `props` 를 그대로 사용할 수 있게 되었습니다.**

    ![pokemon.gif](https://i.imgur.com/IUBX0MN.gif)

    그런데, 이 컴포넌트는 클래스인데, `render` 안에 선언한다는 것은 그냥 함수 안에서 선언하는 거랑 다름 없습니다.. → **굳이 클래스를..?**

### 🤩 이제 함수형 컴포넌트를 사용해봅시다.

```jsx
function ProfilePage({ user }) {
    const showMessage = () => {
        alert('Followed ' + user);
    };

    const handleClick = () => {
        setTimeout(showMessage, 3000);
    };

    return <button onClick={handleClick}>Follow</button>;
}
```

> 클래스의 `this` 와는 다르게, 함수가 받는 인자는 리액트가 변경할 수 없다.

그러기에, 부모 컴포넌트에서 다시 랜더링을 했다해도, **이벤트 핸들러는 이전 `render` 에 종속되어있기 때문에 이전 `props` 를 사용하게 되는 것 입니다.**

즉, 함수형 컴포넌트의 큰 특징은 다음과 같습니다.

-   클래스의 `this` 와는 다르게, 함수가 받는 인자는 리액트가 변경할 수 없다.
-   함수형 컴포넌트는 `render` 될 때의 값들을 유지한다.

```jsx
unction MessageThread() {
  const [message, setMessage] = useState('');

  const showMessage = () => {
    alert('You said: ' + message);
  };

  const handleSendClick = () => {
    setTimeout(showMessage, 3000);
  };

  const handleMessageChange = (e) => {
    setMessage(e.target.value);
  };

  return (
    <>
      <input value={message} onChange={handleMessageChange} />
      <button onClick={handleSendClick}>Send</button>
    </>
  );
}

```

![화면-기록-2021-08-26-오전-11.47.20.gif](https://i.imgur.com/lhv0tIr.gif)

[실행링크](https://codesandbox.io/s/93m5mz9w24)

**이로써 리액트 Hook이라해서, 전혀 다르게 동작하지 않는 다는 것을 알 수 있게 되었습니다.**

### 🙄 그럼 반대로, 최신의 값을 사용하고 싶다면?

알다시피, 클래스형 컴포넌트의 경우 그냥 `this.state` 를 읽어오면 됩니다.

하지만 함수형 컴포넌트는 각 값은 `render` 에 종속된 값을 갖기 때문에 일반 방식으로는 조금 어려울 것 같습니다.

**이는, `useRef` 를 사용하는 방법이 있습니다.**

```jsx
function MyComponent() {
    const ref = useRef(null);
    // `ref.current`로 읽고 쓸 수 있다.
    // ...
}
```

-   **ref →** this처럼 변할 수 있고 서로 다른 `render` 들 끼리 공유할 수 있습니다.

즉, `ref` 는 클래스의 인스턴스 영역과 변경이 가능하다는 점에서 같은 역할을 수행합니다.

하지만, `ref` 는 고정된 값이 ❌ → 랜더링 도중에 읽거나 쓰는 것은 피하는 것이 좋습니다. 왜냐하면 랜더링 내에서는 예측 가능한 일들만 일어나는 것이 권장되기 때문입니다.

**😝 그래서, useEffect를 잘 활용해보자~**

```jsx
function MessageThread() {
  const [message, setMessage] = useState('');

  // 최신값을 쫓아간다
  const latestMessage = useRef('');
  useEffect(() => {
    latestMessage.current = message;
  });

  const showMessage = () => {
    alert('You said: ' + latestMessage.current);
  };

```

`effect` 함수 내부에 `DOM` 이 업데이트 될 때마다 `ref` 값이 변하도록 설정할 수 있습니다. 이와 같이 인터럽트 가능한 랜더링에 의존적인 [Time Slicing and Suspense](https://reactjs.org/blog/2018/03/01/sneak-peek-beyond-react-16.html) 와 같은 기능들이 값의 변경에 의해 피해를 받지 않도록 할 수 있습니다.

**하지만 가장 이상적인 것은 될 수 있으면 `props`나 `state` 를 고정시키는 것이 좋습니다.**

-   `ref` 는 언제 쓰는 것이 좋을까?

    interval이나, subscription과 같은 [명령형 API](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)를 다룰 때, `ref` 가 유용하게 사용될 수 있습니다.

## 출처

이 글은 Dan Abramov의 overreacted의 [함수형 컴포넌트와 클래스, 어떤 차이가 존재할까?](https://overreacted.io/ko/how-are-function-components-different-from-classes/) 를 공부하면서 적은 글 입니다. 자세한 것은 글을 참고해주세요!
