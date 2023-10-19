## 학습 키워드

- Jest
- Describe-Context-It 패턴
- React Testing Library

# Jest

[Jest 공식문서](https://jestjs.io/)

- 거의 모든 것을 갖춘 테스팅 도구.

- Mocha와 Chai처럼 RSpec의 describe-it을 지원하고, expect로 단언(assertion)할 수 있다. Mocking도 다양한 레벨에서 쉽게 사용할 수 있다.

- RSpec이 거의 원조이다.

## 테스트 코드 관련 읽을 거리

- [BETTER SPECS](https://www.betterspecs.org/) → RSpec 베스트 프랙티스 모음. 그대로 쓸 수는 없지만, 참고하자.
- [Ginkgo - Go 언어 개발자를 위한 BDD 테스팅 프레임워크](https://youtu.be/gfTsSBRvdqI) (Go 언어 사례)
- [JUnit5로 계층 구조의 테스트 코드 작성하기](https://johngrib.github.io/wiki/junit5-nested/) \*\*\*\*(Java 언어 사례)
- [Let’s RSpec](https://github.com/ahastudio/til/blob/main/ruby/20161206-rspec-let.md) → Jest는 RSpec의 let 같은 걸 지원하지 않기 때문에, 핵심 아이디어를 가져와서 적당한 수준에서 잘 써야 한다.
- [given-when-then](https://github.com/ahastudio/til/blob/main/blog/2018/12-08-given-when-then.md) → 테스트 코드 작성 템플릿 중 하나

## 테스트 작성해보기

- 파일명 main.test.ts
- npx jest --watchAll ⇒ 코드를 고칠때마다 실행이 됨
- npx jest —verbose ⇒ 좀더 자세히 볼 수 있다.

`TDD`

테스트를 먼저 짜고, 테스트가 통과되는 것을 보면서 코드를 구현함

```tsx
const add = (x: number, y: number): number => {
  return x + y;
};

// 기본적인 테스트 코드
test("숫자 더하기", () => {
  expect(add(1, 2)).toBe(3);
});
```

`BDD`

행동을 묘사함

```tsx
const add=(x:number , y:number):number=>{
	return x+y
}
const context= describe

describe('add 함수는' , () => {
	it('두 숫자의 합을 리턴한다.',()=>{
		expect(add(1,2)).toBe(3)
	})

	context('두개의 양수가 주어졌을 때' , ()=>{
		it('항상 두 개의 숫자보다 큰 값을 돌려준다.' . ()=> {
			expect(add(1,2)).toBeGreaterThan(1);
			expect(add(1,2)).toBeGreaterThan(2);
		})
	})

	context('하나의 양수와 음수가 주어지면' , ()=>{
		it('항상 하나의 양수보다 작은 값을 돌려준다..' . ()=> {
			expect(add(1,-2)).toBeLessThan(1);
		})
	})

})
```

- 할인율을 계산을 하거나 복잡한 것들이 들어가면 유용함
- 할인 쿠폰이 들어가면 더 싸져야함. 이런것들을 막을 수 있음

# React Testing Library

[**React Testing Library 공식문서**](https://testing-library.com/docs/react-testing-library/intro)

[**jest-dom**](https://testing-library.com/docs/ecosystem-jest-dom/)

- UI테스트에 특화된 라이브러리
- E2E처럼 쓸 수 있다.

참고 영상:

- [프론트엔드(Front-end)도 테스트해야 하나요?](https://youtu.be/-kUmsKRmOnA)
- [Mocking 때문에 테스트 코드를 작성하기 어렵나요](https://youtu.be/RoQtNLl-Wko)

```tsx
import {render,screen} from "@testing-library/react"

import Greeting from './Greeting';

test('Greeting' , () => {
	render(<Gretting name="world"/>);

	screen.getByText('Hello world!");
	screen.getByText(/Hello/);
	expect(screen.queryByText(/Hello/)).toBeInTheDocument();
	expect(screen.queryByText(/Hi/)).not.toBeInTheDocument();

	expect(screen.queryByText(/Hi/)).not.toBeVisible();

})
```
