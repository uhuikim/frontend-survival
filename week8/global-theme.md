## 학습 키워드

- Reset CSS
- box-sizing 속성
- word-break 속성
- Theme
- ThemeProvider

### Reset CSS

[Reset Css](https://meyerweb.com/eric/tools/css/reset/)  
[styled-reset](https://github.com/zacanger/styled-reset)

`reset`

브라우저들 마다 스타일이 적용되는 방식이 조금씩 다르다. 브라우저의 내장스타일을 0으로 초기화 해주는 방법

`nomalize`

가능한한 브라우저의 내장 스타일을 최대한 건들지 않는 선에서 브라우저 간에 상이한 부분만 스타일을 통일시켜 준다. CSS Normalize가 적용된 웹페이지는 어느 브라우저에서든 일관적인 모습으로 나타나게 된다.

패키지 설치.

```bash
npm i styled-reset
```

App 컴포넌트에서 사용.

```tsx
import { Reset } from 'styled-reset';

export default function App() {
	return (
		<>
			<Reset />
			<Greeting />
		</>
	);
}
```

### Global Style

`box model`

[대체 CSS box model](https://developer.mozilla.org/ko/docs/Learn/CSS/Building_blocks/The_box_model#대체_css_box_model)

[box-sizing](https://developer.mozilla.org/ko/docs/Web/CSS/box-sizing)

- border-box
- content-box

`rem`

[The 62.5% Font Size Trick](https://www.aleksandrhovhannisyan.com/blog/62-5-percent-font-size-trick/)

일반적인 브라우저는 기본이 16px이다. 폰트 사이즈를 전체에 62.5%를 잡아주면 fontsize가 10px로 변경된다. 그럼 rem을 계산하기가 훨씬 수월해진다.

```jsx
html {
  /* 62.5% of 16px browser font size is 10px */
  font-size: 62.5%;
}

.some-element {
  /* 1.2 * 10px = 12px */
  font-size: 1.2rem;
}
```

`word-break`

[keep-all-villain](https://twitter.com/keepallvillain)

[word-break](https://developer.mozilla.org/ko/docs/Web/CSS/word-break)

### GlobalStyle 만들기

```tsx
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
	// box-sizing을 border-box로 지정한다. 
	html {
		box-sizing: border-box;
	}
	// 
	*,
	*::before,
	*::after {
		box-sizing: inherit;
	}
	
	html {
		font-size: 62.5%;
	}
	
	body {
		font-size: 1.6rem;
	}
	
	:lang(ko) {
		h1, h2, h3 {
			word-break: keep-all;
		}
	}
`;

export default GlobalStyle;
```

```tsx
import { Reset } from 'styled-reset';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
	return (
		<>
			<Reset />
			<GlobalStyle />
			<Greeting />
		</>
	);
}
```

- [createGlobalStyle](https://styled-components.com/docs/api#createglobalstyle)를 사용하여 글로벌 스타일을 만들어주고, APP컴포넌트에서 사용한다.

### Theme

> [Theming](https://styled-components.com/docs/advanced#theming)
> 

> [Create a declarations file](https://styled-components.com/docs/api#create-a-declarations-file)
> 

- 디자인 시스템의 근간을 마련하는데 활용
- 잘 정의하면 다크 모드 등에 대응하기 쉬움.
- 눈에 보이는 단편적인 정보를 넘어서, “의미”에 집중할 수 있게 됨(error, primary…)

1. 일단 기본 Theme부터 정의.

```tsx
const defaultTheme = {
	fonts:{},
	size : {},
	colors: {
		background: '#FFF', // 흰색이 아닌 background색!
		text: '#000',
		primary: '#F00',
		secondary: '#00F',
	},
};

export default defaultTheme;
```

2. App 컴포넌트에서 사용.

```tsx
import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';

import GlobalStyle from './styles/GlobalStyle';

export default function App() {
	return (
		<ThemeProvider theme={defaultTheme}>
			<Reset />
			<GlobalStyle />
			<Greeting />
		</ThemeProvider>
	);
}
```

이제 “props.theme”을 쓸 수 있다.

```tsx
import { createGlobalStyle } from 'styled-components';

const GlobalStyle = createGlobalStyle`
	body {
		background: ${(props) => props.theme.colors.background};
		color: ${(props) => props.theme.colors.text};
	}
	
	a {
		color: ${(props) => props.theme.colors.text};
	}
	
	button,
	input,
	select,
	textarea {
		background: ${(props) => props.theme.colors.background};
		color: ${(props) => props.theme.colors.text};
	}
`;

export default GlobalStyle;
```

3. Type 정의하기

타입 문제를 해결하기 위해 `styled.d.ts` 파일을 작성.

```tsx
import 'styled-components';

declare module 'styled-components' {
	export interface DefaultTheme extends Theme {
		colors: {
			background: string;
			text: string;
			primary: string;
			secondary: string;
		}
	}
}
```

타입을 정의하고 defaultTheme을 맞추는 게 불편하니, 반대로 defaultTheme에서 타입을 추출하자.

```tsx
import defaultTheme from './defaultTheme';

type Theme = typeof defaultTheme;

export default Theme;
```

타입 파일 변경.

```tsx
import 'styled-components';

import Theme from './Theme';

declare module 'styled-components' {
	export interface DefaultTheme extends Theme {}
}
```

다른 theme을 추가할 때 Theme 타입을 사용. 항상 defaultTheme에 먼저 항목을 추가/삭제하고, 나머지를 여기에 맞추면 된다.

```tsx
import Theme from './Theme';

const darkTheme: Theme = {
	colors: {
		background: '#000',
		text: '#FFF',
		primary: '#F00',
		secondary: '#00F',
	},
};

export default darkTheme;
```

### Dark Mode 만들기

의미를 명확히 드러냈다면, 다크 모드를 지원하는 것도 쉽다.

```tsx
import { useDarkMode } from 'usehooks-ts';

import { ThemeProvider } from 'styled-components';

import { Reset } from 'styled-reset';

import defaultTheme from './styles/defaultTheme';
import darkTheme from './styles/darkTheme';

import GlobalStyle from './styles/GlobalStyle';

import Greeting from './components/Greeting';
import Button from './components/Button';

export default function App() {
const { isDarkMode, toggle } = useDarkMode();

const theme = isDarkMode ? darkTheme : defaultTheme;

return (
	<ThemeProvider theme={theme}>
		<Reset />
		<GlobalStyle />
		<Greeting />
		<Button onClick={toggle}>
			Dark Theme Toggle
		</Button>
	</ThemeProvider>
	);
}
```
### Jest 설정

Jest 테스트 쪽에서 “window.matchMedia” 문제 발생.

[Mocking methods which are not implemented in JSDOM](https://jestjs.io/docs/manual-mocks#mocking-methods-which-are-not-implemented-in-jsdom)


`src/setupTests.ts` 파일에 공식 문서에 나온 코드를 넣으면 해결된다.

```tsx
Object.defineProperty(window, 'matchMedia', {
	writable: true,
	value: jest.fn().mockImplementation((query) => ({
		matches: false,
		media: query,
		onchange: null,
		addListener: jest.fn(), // deprecated
		removeListener: jest.fn(), // deprecated
		addEventListener: jest.fn(),
		removeEventListener: jest.fn(),
		dispatchEvent: jest.fn(),
	})),
});
```

### 의미있는 theme 만들기

- https://code.visualstudio.com/api/references/theme-color
- https://getbootstrap.com/docs/5.3/customize/color/