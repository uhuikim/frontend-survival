## 학습 키워드

- useRef
- Hook의 규칙

## useRef

> [beta 문서의 useRef](https://beta.reactjs.org/reference/react/useRef)
> 

> [공식 문서의 useRef](https://ko.reactjs.org/docs/hooks-reference.html#useref)
> 

컴포넌트의 생애주기 전체에 걸쳐서 동일한 객체가 유지된다.. 즉, 컴포넌트가 없어질 때까지 동일한 객체가 유지된다.

`컴포넌트의 생애주기`

컴포넌트가 만들어지고 컴포넌트가 없어질 때 까지를 의미 

- mount : virtual dom으로 들어감
- unmount : virtual dom에서 사라짐

객체 자체가 값은 아니고, 값을 참조하기 위한 객체. 즉, 언제든지 값을 변경할 수 있다.

컴포넌트 밖에서 선언을 하면 여러 컴포넌트가 그 값을 공유한다. 각 컴포넌트가 값을 따로 관리하고 싶으면 컴포넌트 내부에 선언을 해야한다.

상태(state)가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만, 레퍼런스 객체의 현재 값(current)이 바뀌더라도 렌더링에 영향을 주지 않는다.

주요 용도:

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우. ⇒ input 등의 ID 관리.
    
    ```jsx
    const TextField = () => {
    	const id = useRef(Math.random());
    
    	return (
    		<div>
    			<label htmlFor={id.current}>
    				Search
    			</label>
    			<input
    				id={id.current}
    				...
    			/>
    		</div>
    	)
    }
    ```
    
2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우.

Closure → 변수를 capture, bind를 깜빡하는 문제가 종종 일어남.

절대로 쓸 일이 없는 억지로 꾸며낸 상황

```jsx
useEffect(() => {
	setTimeout(() => {
		console.log(filterText);
	}, 5_000);
}, []);
// => 값이 나오지 않는다, 제일 처음 initial 값이 들어있기 때문!

```

값을 바로 가져오려고 하면 값을 capture 하기 때문에 안되지만, ref는 값을 참조하기 위한 객체여서 현재 타이밍에 맞는 값이 제대로 나온다.

```jsx
const query = useRef('')

const [filterText, setFiterText] = useState('')

useEffect(()=>{
	query.current = filterText
},[filterText]);

useEffect(() => {
	setTimeout(() => {
		console.log(query.current);
	}, 5_000);
}, []);
```

위의 예제는 예제를 위한 예제로 이렇게 쓸일을 없을 것이다. 하지만 타이머 다루거나 할때 짜증나는게 많이 일어나기도 한다. callback 자체를 reference에 넣는 등의 방법을 쓰기도 한다.

## Custom Hook

> [Reusing Logic with Custom Hooks](https://beta.reactjs.org/learn/reusing-logic-with-custom-hooks)
> 

> [자신만의 Hook 만들기](https://ko.reactjs.org/docs/hooks-custom.html)
> 

로직을 재사용하기 위한 제일 쉬운 방법.

평범하게 Extract Function을 수행하면 된다. 컴포넌트가 대문자로 시작하는 PascalCase로 이름을 붙인다면, Hook은 “use”로 시작하는 camelCase로 이름을 붙이면 된다.

```jsx
function useFetchProducts() {
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

	return products;
}
```

컴포넌트 코드도 명확해지고, setProducts가 실수로 잘못 쓰일 문제까지 해소됨.

불필요한 것들을 감출 수 있다.

## Hook 규칙

> [Hook의 규칙](https://ko.reactjs.org/docs/hooks-rules.html)
> 

Hook 호출은 규칙이 있어서 단순하게 쓰도록 노력해야 한다.

1. Function Component 바로 안쪽(함수의 최상위)에서만 호출.
2. Function Component 또는 Custom Hook에서만 호출.

처음에는 콜백 함수나 조건문 안에서 Hook을 호출하는 실수를 저지르기 쉽다.

```jsx
if (playing) {
	const products = useFetchProducts();
	console.log(products);
}
```

fetch하는 타이밍을 조절하고 싶을경우! reload 함수를 따로 return을 해서 사용하도록 한다.

```jsx
const {products, fetchProducts} = useFetchProducts();

if (playing) {
	fetchProducts()
}

function useFetchProducts() {
	const [products, setProducts] = useState<Product[]>([]);

// 이렇게 하고 싶으면 확실하게 의존성이 없어야한다!! query에 state를 가져다 쓰는 건 x
	const fetchProducts = async () => {
		const url = 'http://localhost:3000/products';
		const response = await fetch(url);
		const data = await response.json();
		setProducts(data.products);
	};	

	useEffect(() => {
		fetchProducts();
	}, []);

	return {products , fetchProducts };
}
```

커스텀 훅을 잘 쓰면 테스트 코드를 작성할때도 모킹을 많이 해야하는데 모킹을 조금더 수월하게 할 수 도 있고, 테스트 할때도 전체를 하려고 하면 빡쎈데 훅 자체를 unit test를 하면 더 편하다. custom hook을 많이 만들어서 써라!