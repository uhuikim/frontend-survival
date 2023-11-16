## 학습 키워드

- TDD란
- Jest
- Describe - Context - It 패턴
- 단위테스트란


## TDD (Test Driven Development)

> [테스트 주도 개발](https://github.com/ahastudio/til/blob/main/agile/test-driven-development.md)
> 

> [TDD FAQ](https://github.com/ahastudio/til/blob/main/blog/2016/12-03-tdd-faq.md)
> 

> [Jest를 이용한 간단한 TDD 예제](https://github.com/ahastudio/til/blob/main/jest/20201204-simple-tdd-example.md)
> 

<aside>
💡 테스트 코드를 먼저 작성하는, 즉 구현보다 인터페이스와 스펙을 먼저 정의함으로써 개발을 진행하는 방식.

</aside>

- `인터페이스& 스펙 정의하기` : 더하기 함수 만들기
    
     add(x,y) → number x,y를 넣으면 값으로 number가 나올꺼다~
    
    이런게 있고, 실행하면 이렇게 된다를 먼저 정의를 해둔다.
    
- TDD를 만든 사람은 켄트 벡이라는 사람이다.

**TDD Cycle**

1. Red → 실패하는 테스트 코드를 작성. 인터페이스와 스펙에 집중한다.
2. Green → 재빨리 테스트를 통과시킨다. 올바른 방법이 아니어도 괜찮다.
3. Refactor → 리팩터링을 통해 코드를 올바르게 만든다. TDD에서 가장 중요한 부분이지만, 간과될 때가 많다.

작은 단계를 찾고, 코드에서 피드백을 얻는 게 (어렵고) 중요하다. 2번이 어렵다면 1번으로 돌아가서 더 작고 쉬운 문제를 정의하고, 3번을 위해 의도를 드러내고 중복을 찾아 제거하는 연습을 해야 한다. 이 둘이 익숙하지 않으면 TDD를 하는 게 거의 불가능하고, 사실 이 둘이 어려우면 일반적인 개발 또는 클린 코드를 작성하는 것 또한 매우 힘들다.

## Jest

> [Jest](https://jestjs.io/)
> 

> [Given-When-Then](https://github.com/ahastudio/til/blob/main/blog/2018/12-08-given-when-then.md)
> 
- 페이스북에서 만든 테스트 도구
- 파일명은 BDD 스타일일 경우 **.spec.ts을 사용하기는 하지만 **.test.ts로 통일해서 사용해도 된다.

테스트 케이스를 정의할 때 크게 두 가지 방법을 사용한다:

1. test 함수로 개별 테스트를 나열하는 방식.
2. BDD 스타일로 주체-행위 중심으로 테스트를 조직화하는 방식.

처음에는 test 함수로 개별 테스트를 써보자.

```jsx
test('add', () => {
	expect(add(1, 2)).toBe(3);
});
```

BDD 스타일로 테스트 대상과 행위를 명확히 드러내자. 어떤것을 테스트 할것이다, 어떻게 될 것이다를 보여줄 수 있도록!

```jsx
describe('add', () => {
	it('returns sum of two numbers', () => {
		expect(add(1, 2)).toBe(3);
	});
});

// describe "add"는 주어가 된다.
// it => add가 어떻게 된다를 표현한다.
```

다양한 경우를 고려해 보자.

```jsx
function add(...numbers : number[]):number{
	if(numbers.length === 0) {
		return 0;
	}
	if(numbers.length === 1) {
		return numbers[0];
	}
	if(numbers.length === 2) {
		return numbers[0] + numbers[1];
	}
	if(numbers.length === 3) {
		return numbers[0] + numbers[1] + numbers[2];
	}
}

// 위 함수의 중복을 제거하고 아래와 같이 수정함
function add(...numbers : number[]):number{
	if(numbers.length === 0) {
		return 0;
	}
	// return add(...numbers.slice(0, numbers.length -1)) + numbers[numbers.length -1]
 return numbers.reduce((acc,number) => acc+number) 
}

```

```jsx

const context = describe;

describe('add', () => {
	context('with no argument', () => {
		it('returns zero', () => {
			expect(add()).toBe(0);
		});
	});

	context('with only one number', () => {
		it('returns the same number', () => {
			expect(add(1)).toBe(1);
		});
	});

	context('with two numbers', () => {
		it('returns sum of two numbers', () => {
			expect(add(1, 2)).toBe(3);
		});
	});

	context('with three numbers', () => {
		it('returns sum of three numbers', () => {
			expect(add(1, 2, 3)).toBe(6);
		});
	});
});
```

Jest에서 TypeScript 사용하도록 `jest.config.js` 파일 작성

```jsx
module.exports = {
	testEnvironment: 'jsdom',
	setupFilesAfterEnv: [
		'@testing-library/jest-dom/extend-expect',
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

# **Matchers**

**Common Matchers[](https://jestjs.io/docs/using-matchers#common-matchers)**

- toBe
- toEqual
- toStrictEqual

**Truthiness**

- toBeNull
- toBeUndefined
- toBeDefined
- toBeTruthy
- toBeFalsy

**Numbers**

- toBeGreaterThan
- toBeGreaterThanOrEqual
- toBeLessThan
- toBeLessThanOrEqual
- toBeCloseTo

**Strings**

- toMatch

**Arrays and iterables**

- toContain

**Exceptions**

- toThrow