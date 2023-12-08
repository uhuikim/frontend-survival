## 학습 키워드

- className
- 의미있는 마크업

## Class로 스타일링하기

[스타일링과 CSS](https://ko.reactjs.org/docs/faq-styling.html)

`index.html` 파일에 간단히 CSS 추가

```tsx
<style>
	.greeting {
		color: #00F;
	}
</style>
```

`className` 지정

```tsx
export default function Greeting() {
	return (
		<p className="greeting">
			Hello, world!
		</p>
	);
}
```

css를 작성하는 규칙을 만들어서 사용한다. 

- SMACSS

## Inline Style 로 스타일링하기

- style 속성 활용.
- 평범한 JavaScript 객체를 활용하므로 변수, 함수 등을 재사용하기 쉽다.
- 텍스트가 아니라서 실수하거나 불편할 때가 있다.
- TypeScript의 힘으로 자동완성, 타입 검사 등의 도움을 받을 수 있다.

```tsx
export default function Greeting() {
	// 자바스크립트로 처리할 수 있다는 장점이 있다.
	fuction primaryColor(){
		return darkMode ? "F00" : "#OOF"
	}
	const style = {
		color: '#00F',
	};
	
	return (
		<p style={style}>
			Hello, world!
		</p>
	);
}
```

바로 쓸 수도 있다.

```tsx
export default function Greeting() {
	return (
		<p
			style={{
				color: '#00F',
			}}
		>
			Hello, world!
		</p>
	);
}
```

# 의미있는 마크업

[HTML 참고서](https://developer.mozilla.org/ko/docs/Web/HTML/Reference)

의미있는 마크업을 하기 위해서는 HTML Element 들이 어떤 의미를 가지고 있는지 정확하게 알고있어야 한다.

의미있는 마크업 기준에서 tailwind의 classname에 font-size-16를 적는 것은 맞지 않을 수 있다. font size가 몇 인지는 의미가 있는 것이 아니다.