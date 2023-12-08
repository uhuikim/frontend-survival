## 학습 키워드

- styled-componets

## styled-components

> [styled-components](https://styled-components.com/)
> 

> [Babel Plugin](https://styled-components.com/docs/tooling#babel-plugin)
> 

> [@swc/plugin-styled-components](https://github.com/swc-project/plugins/tree/main/packages/styled-components)
> 

- 스타일이 적용된 컴포넌트를 쉽게 만들 수 있는 도구.
- [VS Code Extension](https://www.notion.so/styled-components-73b87e6c5eee4e15a057016fbf2a995e?pvs=21)을 설치해서 쓰기
- Babel Plugin을 SWC에서 쓸 수 있도록 포팅한 것도 함께 설치(SSR 지원 등을 위한 공식 권장사항).

```bash
npm i styled-components
npm i -D @types/styled-components @swc/plugin-styled-components
```

`.swcrc` 파일 작성.

```tsx
{
	"jsc": {
		"experimental": {
			"plugins": [
				[
					"@swc/plugin-styled-components",
					{
						"displayName": true,
						"ssr": true
					}
				]
			]
		}
	}
}
```

- 간단한 Styled Component 만들기.

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
	color: #00F;
`;

export default function Greeting() {
	return (
		<Paragraph>
			Hello, world!
		</Paragraph>
	);
}
```

- Styled Component에 추가로 스타일을 입히는 것도 가능하다.

```tsx
import styled from 'styled-components';

const Paragraph = styled.p`
	color: #00F;
`;

const BigParagraph = styled(Paragraph)`
	font-size: 5rem;
`;

export default function Greeting() {
	return (
		<BigParagraph>
			Hello, world!
		</BigParagraph>
	);
}
```

- 기존 컴포넌트에 스타일을 입히는 것도 가능. 단, 기존 컴포넌트가 Class를 잡아줘야 한다.

```tsx
import styled from 'styled-components';

function HelloWorld({ **className** }: React.**HTMLAttributes**<HTMLElement>) {
	return (
		<p className={className}>
			Hello, world!
		</p>
	);
}

const Greeting = styled(HelloWorld)`
	color: #00F;
`;

export default Greeting;
```