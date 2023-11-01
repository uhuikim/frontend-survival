dynamic하게 작동하게 하려면 state가 필요하다. 

지난 시간에 정적으로 만든것을 동적으로 만들어보는 시간이다.

## Thinking in React

> [Thinking in React](https://beta.reactjs.org/learn/thinking-in-react)
> 
- “Step 3: Find the minimal but complete representation of UI state” (상태를 찾아라)
- “Step 4: Identify where your state should live”(어디에 있게 할것인가)
- “Step 5: Add inverse data flow”

## React의 State

데이터가 있다고 다 state가 되는 것은 아니다!

React의 state는 “변경”을 다루기 위한 요소. “Re-rendering”이란 주제에서 다뤄진다. 거칠게 이야기하면, 어떤 컴포넌트의 state가 바뀌면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하게 된다.

아무렇게나 막 만들어도 되지만, 일관성과 효율을 위해 DRY 원칙을 따르는 SSOT를 만든다.

- [DRY (Don’t Repeat Yourself)](https://ko.wikipedia.org/wiki/중복배제) ⇒ 리액트 문서에 dry원칙을 따르라 나와있다.중복하지 마라라!
- [SSOT (Single Source of Truth)](https://ko.wikipedia.org/wiki/단일_진실_공급원)

React State의 조건:

1. 변경돼야 함. 변경되지 않는 건 state로 다룰 가치가 없다.
2. 부모 컴포넌트가 props를 통해 전달한다면 state가 아님.
3. 다른 state나 props를 이용해 계산 가능하다면 state가 아님.

```jsx
const [categories , setCategories] = useState([])

useEffect(()=>{
	setCategories(selectVategories(products))
},[])
// 이렇게 쓰지 마라

```

- 상태가 아닌 것은 [상태처럼 관리하지 마라](https://react.dev/learn/you-might-not-need-an-effect)

`상태가 너무 많아지면?`

다루는 상태가 너무 많으면 복잡함. TypeScript를 잘 쓰면 직접 관리하는 상태의 수를 줄여줄 수 있다.

자바스크립트는 모아놨을때 그 안에 뭐가 들어있는지 몰라서 엄청 불안하다. props로 한개씩 분리해서 던져주는게 안전하다. 타입스크립트에서는 하나만 던져도 타입을 잘 정의를 해주면 된다. 

⇒ 타입스크립트를 리액트에서 잘 쓰는 방법을 고민할 필요가 있다. 

## state의 위치

그렇다면 그 상태를 누가 관리해야 할까? 더 정확히는, 상태를 누가 소유해야 할까?

(React만 쓴다면) 해당 상태에 의존적인 컴포넌트를 모두 포함하는 컴포넌트가 상태를 소유해야 함.

- [“Lifting State Up”](https://ko.reactjs.org/docs/lifting-state-up.html)
- [Sharing State Between Components](https://beta.reactjs.org/learn/sharing-state-between-components)

⇒ state를 상위로 끌어올림 , 공통으로 되는 곳이 있을 때 까지

## Inverse Data Flow

하위 컴포넌트의 props로 함수를 전달. 흔히 콜백 함수라고 부름.

TypeScript(정확히는 JavaScript)는 함수가 일급(first-class) 객체다. 즉, 어떤 함수를 다른 함수에 인자로 넘겨주거나, 어떤 함수를 리턴값으로 사용할 수 있다. 익명 함수, 클로저 등과 함께 사용하면 시너지가 큼.

- [일급 함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
- 다시 [“Lifting State Up”](https://ko.reactjs.org/docs/lifting-state-up.html)