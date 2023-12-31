
## 학습 키워드

- Service worker
- MSW(Mock Service Worker)
- polyfill(폴리필)

## MSW (Mock Service Worker)

> [MSW](https://mswjs.io/)
> 

> [Service Worker API](https://developer.mozilla.org/ko/docs/Web/API/Service_Worker_API)
> 

> [아샬의 Mock Service Worker (MSW)](https://github.com/ahastudio/til/blob/main/mock-api/msw.md)
> 

> [Mocking REST API](https://mswjs.io/docs/getting-started/mocks/rest-api)
> 

> [Integrate mocking into Node](https://mswjs.io/docs/getting-started/integrate/node)
> 

- 브라우저에서 쓸 수 있는 . 오프라인 작업 등을 지원하기 위한  service worker를 이용함.
- 기존 코드는 코드레벨에서 가짜로 데이터를 보내주도록 구현했었는데 msw는 네트워크 레벨에서 가짜로 구현한다.

MSW 패키지 설치

```bash
npm i -D msw
```

`jest.config.js` 파일의 “setupFilesAfterEnv” 속성에 `setupTests.ts` 파일 추가.

```jsx
module.exports = {
	testEnvironment: 'jsdom',
	**setupFilesAfterEnv**: [
		'@testing-library/jest-dom/extend-expect',
		'**<rootDir>/src/setupTests.ts**',
	],
	transform: {
		'^.+\\.(t|j)sx?$': ['@swc/jest', {
			jsc: {
				parser: {
					syntax: 'typescript',
					jsx: true,
					decorators: true,
				},
				transform: {
					react: {
						runtime: 'automatic',
					},
				},
			},
		}],
	},
};
```

`src/setupTests.ts` 파일

```jsx
import server from './mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

afterAll(() => server.close());

afterEach(() => server.resetHandlers());
```

`src/mocks/server.ts` 파일

```jsx
import { setupServer } from 'msw/node';

import handlers from './handlers';

const server = setupServer(...handlers);

export default server;
```

- msw를 개발용으로만 설치함, 그러면 test 등에서만 사용할 수 있다고 lint에러가 발생한다. 따라서 mocks 폴더안에 .eslintrc.js 파일을 만들어주면 좋다.

 

`src/mocks/handlers.ts` 파일

→ Express의 경험을 살려보자!

```jsx
import { rest } from 'msw';

const BASE_URL = 'http://localhost:3000';

const handlers = [
	rest.get(`${BASE_URL}/products`, (req, res, ctx) => {
		const products = [
			{
				category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
			},
		];

	 // const {products} = fixtures;

		return res(
			ctx.status(200),
			ctx.json({ products }),
			);
		}),
	];

export default handlers;
```

`App.test.ts` 파일

```jsx
import { render, screen, waitFor } from '@testing-library/react';

import App from './App';

// jest.mock 불필요.

test('App', async () => {
	render(<App />);
	
	await waitFor(() => {
		screen.getByText('Apple');
	});
});
```

- 렌더링 한다음에 바로 테스트를 하게 되면 실패를 한다. 데이터를 fetch 해올때까지 기다려야한다.
- 조금 기다렸다가 이값이 나오면 돼! ⇒ waitFor

너무 본격적으로 코딩하면 사실상 백엔드를 개발하게 되니, 이 부분에 주의할 것.

테스트 환경(Node.js 기반) 외에 웹 브라우저도 지원하기 때문에, API 스펙은 나왔지만 아직 구현되지 않은 경우 임시로 사용할 수도 있다. 단순히 임시 서버를 만들 거라면 Express를 쓰는 게 더 낫지만, 테스트 코드도 지원하면서 겸사겸사 웹 브라우저를 지원하는 용도로는 나쁘지 않은 선택이다.

최신 node에는 fetch가 들어왔다. fetch가 브라우저에서는 되는데 node에서는  지원이 안되는 경우가 있다. github의 fetch polyfill을 설치해준다. 

- [GitHub에서 만든 fetch polyfill](https://github.com/github/fetch)

```jsx
npm install whatwg-fetch --save
```

```jsx
// setupTest.ts
import 'whatwg-fetch';
import server from './mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));

afterAll(() => server.close());

afterEach(() => server.resetHandlers());
```