# Navigation

## 학습 키워드

- Web APIs - History
- React Router - NavLink, Link, Navigate, useNavigate


지금은 페이지를 변경했을 때 매번 전체를 새로고침을 하고 있다. 

전체를 갱신하지 않고 일부만 갱신하고 싶고, 기존에 처리하던 것을 유지를 하고 싶을때 특별하게 네비게이션 처리를 해줘야한다.

### History.pushState

> [History.pushState()](https://developer.mozilla.org/ko/docs/Web/API/History/pushState)
> 

html5부터는 history pushState가 생겼다.

이전에는 #(hash) or #!(hash bang)을 붙여서 이동시켜서 새로 로딩을 하지 않도록 구현했었다. 

```jsx
export default function Header(){
	// url만 변경시켜준다. 
	const handleClick = (e : SyntheticEvent) => {
		e.preventDefault();
		const state = {};
		const title = '';
		const url = '/about';
		history.pushState(state, title, url);
	}

	return (
		<header>
			<nav>
				<ul>
					<li>
						<a href="/" onClick={handleClick}>Home</a>
					</li>
					<li>
						<a href="/about" onClick={handleClick}>About</a>
					</li>
				</ul>
			</nav>
		</header>
	)
}
```

### Link

> [Link](https://reactrouter.com/en/main/components/link)
> 

react router dom에서 제공해주는 Link, url 을 변경해주고 내용도 알맞게 바꿔준다!

```tsx
function Header() {

return (
	<header>
		<nav>
			<ul>
				<li><Link to="/">Home</Link></li>
				<li><Link to="/about">About</Link></li>
			</ul>
		</nav>
	</header>
	);
}
```

### NavLink

> [NavLink](https://reactrouter.com/en/main/components/nav-link)
> 
- 링크 클릭시 class에 active를 넣어준다.

```tsx
function Header() {
	return (
		<header>
			<nav>
				<ul>
					<li><NavLink to="/">Home</NavLink></li>
					<li><NavLink to="/about">About</NavLink></li>
				</ul>
			</nav>
		</header>
	);
}
```

### Navigate

> [Navigate](https://reactrouter.com/en/main/components/navigate)
> 

```tsx
import { Navigate } from 'react-router-dom';

export default function LoginPage() {
	return (
		<Navigate to="/" />
	);
}
```

테스트에서 “ReferenceError: Request is not defined” 에러가 나면 “whatwg-fetch”를 임포트해서 해결할 수 있다.

### useNavigate

> [useNavigate](https://reactrouter.com/en/main/hooks/use-navigate)
> 

```tsx
import { useNavigate } from 'react-router-dom';

export default function Footer() {
	const navigate = useNavigate();
	
	const handleClick = () => {
		navigate('/about');
	};
	
	return (
		<footer>
			<button type="button" onClick={handleClick}>
				Press
			</button>
		</footer>
	);
}
```