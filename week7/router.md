# Router

## 학습 키워드

- ReactRouter
- RouterProvider

## routes정의하기

```jsx
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

- <Routes> 컴포넌트는 props.children를 재귀적으로 반복하고 소품을 제거한 후 다음과 같은 객체를 생성합니다:

```jsx
let routes = [
  {
    element: <App />,
    path: "/",
    children: [
      {
        index: true,
        element: <Home />,
      },
      {
        path: "teams",
        element: <Teams />,
        children: [
          {
            index: true,
            element: <LeagueStandings />,
          },
          {
            path: ":teamId",
            element: <Team />,
          },
          {
            path: ":teamId/edit",
            element: <EditTeam />,
          },
          {
            path: "new",
            element: <NewTeamForm />,
          },
        ],
      },
    ],
  },
  {
    element: <PageLayout />,
    children: [
      {
        element: <Privacy />,
        path: "/privacy",
      },
      {
        element: <Tos />,
        path: "/tos",
      },
    ],
  },
  {
    element: <Contact />,
    path: "/contact-us",
  },
];
```

### DATA-APIS

v6.4에서는 새로운 [데이터 API](https://reactrouter.com/en/main/routers/picking-a-router#data-apis)를 지원하는 새로운 라우터가 도입되었습니다.

- `[createBrowserRouter](https://reactrouter.com/en/main/routers/create-browser-router)`
- `[createMemoryRouter](https://reactrouter.com/en/main/routers/create-memory-router)`
- `[createHashRouter](https://reactrouter.com/en/main/routers/create-hash-router)`
- `[createStaticRouter](https://reactrouter.com/en/main/routers/create-static-router)`

모든 웹 프로젝트는 **createBrowserRouter**를 사용하는 것이 좋습니다.

## createBrowserRouter

- 기존 App코드는 Layout 과 라우팅 2가지를 처리해주고 있다. Layout과 routing을 별도로 분리를 해보자
- 라우터 객체 만들어서 사용하기
- [RouterProvider](https://reactrouter.com/en/main/routers/router-provider)로 넘겨주기

```tsx
import { Outlet } from 'react-router-dom';

function Layout() {
	return (
		<div>
			<Header />
			<Outlet />
			<Footer />
		</div>
	);
}

const routes = [
	{
		element: <Layout />,
		children: [
			{ path: '/', element: <HomePage /> },
			{ path: '/about', element: <AboutPage /> },
		],
	},
];

export default routes;
```

```tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

import routes from './routes';

const router = createBrowserRouter(routes);

root.render((
	<React.StrictMode>
		<RouterProvider router={router} />
	</React.StrictMode>
));
```

## createMemoryrouter

메모리 라우터 만들어서 테스트.

- 기존 App에 대한 테스트를 routes에 대한 테스트로 변경한다.
- 이제는 routes에서 컴포넌트, 레이아웃에 대한 정보를 가지고 있다.

```tsx
// routes.test.tsx

describe('routes', () => {	
	function renderRouter(path: string) {
		const router = createMemoryRouter(routes, { initialEntries: [path] });
		render(<RouterProvider router={router} />);
	}
	
	context('when the current path is “/”', () => {
		it('renders the home page', () => {
			renderRouter('/');
	
			screen.getByText(/Hello/);
		});
	});
	
	context('when the current path is “/about”', () => {
		it('renders the about page', () => {
			renderRouter('/about');
	
			screen.getByText(/About/);
		});
	});
});
```