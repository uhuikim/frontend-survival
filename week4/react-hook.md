## 학습 키워드

- React Hook 이란
- Hooks
    - useState
    - useEffect
    - useContext
    - useRef
    - useLayoutEffect
- React StrictMode 란

## React의 Hook

> [Hook의 개요](https://ko.reactjs.org/docs/hooks-intro.html)
> 

> [Hook 개요](https://ko.reactjs.org/docs/hooks-overview.html)
> 

> [Hooks API Reference](https://ko.reactjs.org/docs/hooks-reference.html)
> 

React 16.8에서 Hooks가 도입됨. 기존 방식에 있던 몇 가지 문제를 해결.

react 18에서는 concurrente 모드가 강조가 되었다. 

[React Conf 2018 Hooks 소개 영상](https://youtu.be/dpw9EHDh2bM)

Hooks 이전 방식의 문제점:

- Wrapper Hell (HoC) : 고차 컴포넌트
- Huge Components
- Confusing Classes

[HoC (Higher-Order Components)](https://ko.reactjs.org/docs/higher-order-components.html)

React를 쓰는 방식을 완전히 바꾼 커다란 변화.

→ 이제는 예전으로 돌아가는 게 불가능하다!

기존:

- 상태를 가진 컴포넌트는 Class Component로 만들고, props만 사용하는 재사용이 용이한 작은 컴포넌트는 Function Component로 작성.
- Redux에서도 비슷한 구분이 존재했다.
    - [Presentational and Container Components - Dan Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

현재:

- 그냥 Function Component만 사용.
- 상태 관리 유무를 바로 알기 어려움 = 신경쓰지 않아도 됨.
- 복잡한 요소는 전부 Hook으로 격리 및 재사용 가능.

대표적인 Hooks

- useState → State Hook ⇒ React의 State
- useEffect ⇒ Side-effect
- useContext
- useRef
- useLayoutEffect → useEffect와 조금 다름.

## useEffect

> [Synchronizing with Effects](https://beta.reactjs.org/learn/synchronizing-with-effects)
> 

> [You Might Not Need an Effect](https://beta.reactjs.org/learn/you-might-not-need-an-effect)
> 

> [Using the Effect Hook](https://ko.reactjs.org/docs/hooks-effect.html)
> 

> [useEffect](https://beta.reactjs.org/reference/react/useEffect)
> 

> [useEffect 완벽 가이드](https://overreacted.io/ko/a-complete-guide-to-useeffect/)
> 

- 외부 상태와 싱크를 해야할 때
- useEffect를 정말 쓸때와 아닐때를 구분해야한다.

렌더링 이후 해야 할 일, 즉 React의 외부와 관련된 일을 정해줄 수 있다.

렌더링이 어느것인가? 화면에 그리는 것이냐? 아니면 dom tree를 구성하는 것이냐? 언제 타이밍인지는 잘 모른다.

기본적으로 렌더링 때마다 실행되므로, 의존성 배열을 통해 언제 이펙트를 실행할지 지정할 수 있다(= 불필요한 경우에 건너뛸 수 있다).

함수를 리턴함으로써 종료 처리를 할 수 있다.

### 타이머 예제

React의 외부에 우아하게 접근. 이 정도는 useEffect를 안 쓴다고 크게 문제가 되지 않지만, 이렇게 쓰는 습관을 들이자.

```jsx
useEffect(() => {
	document.title = `Now: ${new Date().getTime()}`;
});
```

타이머를 on/off하는 기능을 그냥 만들면 문제가 발생한다.

```jsx
function Timer() {
	useEffect(() => {
		setInterval(() => {
			document.title = `Now: ${new Date().getTime()}`;
		}, 100);
	});

	return (
		<p>Playing</p>
	);
}

export default function TimerControl() {
	const [playing, setPlaying] = useState(false);
	
	const handleClick = () => {
		setPlaying(!playing);
	};

	return (
		<div>
			{playing ? (
				<Timer />
			) : (
				<p>Stop</p>
			)}
			<button type="button" onClick={handleClick}>
				Toggle
			</button>
		</div>
	);
}
```

⇒ 위의 코드는 상태를 stop로 설정했어도 계속해서 타이머가 돌아가는 것을 볼 수 있다. 

컴포넌트를 없앴을때의 처리를 추가해야한다. 

종료 처리

```jsx
useEffect(() => {
	const savedTitle = document.title;

	const id = setInterval(() => {
		document.title = `Now: ${new Date().getTime()}`;
	}, 100);

	return () => {
		document.title = savedTitle;
		clearInterval(id);
	};
});
```

### 처음에 한번만 실행하기

의존성 배열에서 아무 것도 지정하지 않으면 맨 처음에 딱 한번만 실행하게 할 수 있다.

주로 API를 호출해서 데이터를 얻을 때 사용한다.

```jsx
const [products, setProducts] = useState<Product[]>([]);

useEffect(() => {
	const fetchProducts = async () => {
		const url = 'http://localhost:3000/products';
		const response = await fetch(url);
		const data = await response.json();
		setProducts(data.products);
	};

	fetchProducts();
}, []);
```

Fetch 함수의 위치가 고민된다면, Dan Abramov의 글을 다시 보자.

- [useEffect 완벽가이드 - 함수를 이펙트 안으로 옮기기](https://overreacted.io/ko/a-complete-guide-to-useeffect/#%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%ED%8E%99%ED%8A%B8-%EC%95%88%EC%9C%BC%EB%A1%9C-%EC%98%AE%EA%B8%B0%EA%B8%B0)

⇒ useEffect의 fetchProducts를 밖으로 빼는건 x , useEffect안에서 사용해라!

### Effect가 두 번 실행되는 문제

<React.StrictMode>로 컴포넌트 전체를 감쌀 경우, 예상치 못한 Side Effect를 찾으려고 Effect 등을 두 번씩 실행함. 개발 모드에서만 활성화가 되어 2번씩 실행되지만, 프로덕션에서는 상관이 없다.

아샬님은 <React.StrictMode>를 사용하는 것을 강력히 추천한다.

- [예상치 못한 부작용 검사](https://ko.reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)

### 의존성 배열을 이용해 Fetch할 때 주의사항

- [Fetching data](https://beta.reactjs.org/learn/synchronizing-with-effects#fetching-data)

page를 바꿨을 때 새로 데이터를 읽어온다. 이전에 읽어오던것을 멈출수 없다. 최근에는 cancel시키는 것이 들어오긴 했다. 응답이 누가 더 빨리 올지 모른다. 먼저 실행했다고 먼저 응답이 오는게 아니다.  아래와 같이 처리를 할 수 있다. 

```jsx
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```