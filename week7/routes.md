# Routes

## 학습 키워드

- 라우터란?
- React Router
    - Browser Router
    - Route
    - Memory Router


## React Router

react router는 "client side routing"을 가능하게 한다.

```jsx
npm i react-router-dom
```

- 개발용으로만 쓰는게 아니라서 -D를 붙이지 않는다.

### React Router의 세 가지 주요 작업

1. Subscribing and manipulating the [history stack](https://reactrouter.com/en/main/start/concepts#history-stack)
2. Matching the [URL](https://reactrouter.com/en/main/start/concepts#url) to your [routes](https://reactrouter.com/en/main/start/concepts#route-config)
3. Rendering a nested UI from the [route matches](https://reactrouter.com/en/main/start/concepts#matches)

`history stack`

브라우저는 사용자가 탐색할 때 자체 history stack을 유지합니다. 이것이 바로 뒤로 가기 및 앞으로 가기 버튼이 작동하는 방식입니다. 

기존 웹사이트(자바스크립트가 없는 HTML 문서)에서는 사용자가 링크를 클릭하거나 양식을 제출하거나 뒤로 및 앞으로 버튼을 클릭할 때마다 브라우저가 서버에 요청을 보냅니다.

 

`client side routing`

```jsx
// push an entry into the browser history stack and change the URL
window.history.pushState({}, undefined, "/contact");
```

위의 방법으로 서버 요청없이 url을 변경할 수 있다. 하지만 UI는 변경할 수 없다.

브라우저 창에서 앞으로가기 위로가기 클릭하는 것은 addEventListener로 감지할 수 있는데, 개발자가 사용한window.history.pushState 를 감지할 수 있는 방법은 없다. react router는 이를 제공해준다. 

history stack이 변경되는것을 구독하고 url 변경에 맞는 UI를 제공해준다.

`Locations`

- 리액트 라우터에서 사용하는 location, window.location과 유사하다.

```jsx
{
  pathname: "/bbq/pig-pickins",
  search: "?campaign=instagram",
  hash: "#menu",
  state: null,
  key: "aefz24ie"
}
```

- location.state
    - history에 있는 state값을 활용한 location.state!
        
        ```jsx
        window.history.pushState("look ma!", undefined, "/contact");
        window.history.state; // "look ma!"
        // user clicks back
        window.history.state; // undefined
        // user clicks forward
        window.history.state; // "look ma!"
        ```
        
    - location.state는 URL에서 숨겨진다.
    - 다음페이지의 사용자가 어디에서 왔는지를 알려줄때 많이 사용된다.
        
        ```jsx
        <Link to="/pins/123" state={{ fromDashboard: true }} />;
        
        let navigate = useNavigate();
        navigate("/users/123", { state: partialUser });
        ```
        

### Routes

> [Routes](https://reactrouter.com/en/main/components/routes)
> 

> [Route](https://reactrouter.com/en/main/route/route)
> 

간단히 코드 옮기기.

```tsx
import { Routes, Route } from 'react-router-dom';

function App() {
	return (
		<div>
			<Header />
			<main>
				<Routes>
					<Route path="/" element={<HomePage />} />
					<Route path="/about" element={<AboutPage />} />
				</Routes>
			</main>
			<Footer />
		</div>
	);
}
```

브라우저 라우터 내려주기.

> [BrowserRouter](https://reactrouter.com/en/main/router-components/browser-router)
> 

```tsx
import { BrowserRouter } from 'react-router-dom';

root.render((
	<BrowserRouter>
		<App />
	</BrowserRouter>
));
```

- window는 브라우저에만 있다. 웹 브라우저 환경에서 쓰는 `BrowserRouter`를 사용한다.
- App안에서 BrowserRouter 를 쓰지 않고 main에다가 써줬다. 우리는 App을 테스트 할꺼기 때문에! 테스트 환경에서는 BrowserRouter를 쓰면 실패한다.

테스트 코드에선 메모리 라우터 사용.

> [MemoryRouter](https://reactrouter.com/en/main/router-components/memory-router)
> 

```tsx
describe('App', () => {
	function renderApp(path: string) {
		render((
			<MemoryRouter initialEntries={[path]}>
				<App />
			</MemoryRouter>
		));
	}
	
	context('when the current path is “/”', () => {
		it('renders the home page', () => {
			renderApp('/');

			screen.getByText(/Hello/);
		});
	});
	
	context('when the current path is “/about”', () => {
		it('renders the about page', () => {
			renderApp('/about');

			screen.getByText(/About/);
		});
	});
});
```