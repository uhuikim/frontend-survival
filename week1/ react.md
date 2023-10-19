# 학습 키워드

- React란?
- React 컴포넌트
- React 리렌더링
- IoC(Inversion of Control)
- Library vs Framework

## 리액트 관련 문서

- [React Beta](https://beta.reactjs.org/) 버전 문서
- [React 공식문서](https://ko.reactjs.org/) 옛날 버전

- React로 작업하는 프로세스는 [Thinking in React](https://beta.reactjs.org/learn/thinking-in-react)를 참고. “상태”를 골라내는 게 핵심이다.
- 한국어로 읽고 싶다면 [예전 문서의 설명](https://ko.reactjs.org/docs/thinking-in-react.html)만 살짝 참고하자(코드는 참고하지 말 것!).
- [React 코어 개발자가 쓴 React에 대한 이해를 돕는 글](https://overreacted.io/ko/react-as-a-ui-runtime/) (필독!)

## 렌더링

- [createRoot](https://beta.reactjs.org/reference/react-dom/client/createRoot) 로 렌더링을 해준다
  - 한국어로 읽고 싶다면 [예전 문서](https://ko.reactjs.org/docs/react-dom-client.html#createroot)를 아주 살짝 참고하자.
- root를 여러개 만들어줘도 된다.
- 여러번 render를 해줘도 된다.

  ```tsx
  function Greeting({ count }) {
    return <p>Hello, world! {count}</p>;
  }

  function main() {
    const element = document.getElementById("root");

    if (!element) {
      return;
    }

    const root = ReactDOM.createRoot(element);

    let count = 0;
    root.render(<Greeting count={count} />);

    setInterval(() => {
      count += 1;
      root.render(<Greeting count={count} />);
    }, 1_000);
  }

  main();
  ```

  중첩되는게 아니라 있는 것을 업데이트를 한다.
  전체를 업데이트 하는게 아니라 , 필요한 부분만 업데이트한다.

  - [updating-a-root-component](https://beta.reactjs.org/reference/react-dom/client/createRoot#updating-a-root-component)

## 리렌더링

- [React는 컴포넌트를 언제 다시 리렌더링 할까요?](https://velog.io/@surim014/react-rerender)
- [왜 리액트에서 리렌더링이 발생하는가.](https://medium.com/@yujso66/%EB%B2%88%EC%97%AD-%EC%99%9C-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90%EC%84%9C-%EB%A6%AC%EB%A0%8C%EB%8D%94%EB%A7%81%EC%9D%B4-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94%EA%B0%80-74dd239b0063)
- [React 렌더링 동작에 대한 (거의) 완벽한 가이드](https://velog.io/@superlipbalm/blogged-answers-a-mostly-complete-guide-to-react-rendering-behavior)

- state가 바뀔때
- 부모가 변경되면 자녀들도 리렌더링

## 읽을거리

[면접 때 종종 나오는 (쓸모 없는) 질문인 “React는 프레임워크인가요, 라이브러리인가요?”에 대한 React 개발자의 답변](https://twitter.com/trueadm/status/1194567962784653312)
[제어의 역전](https://martinfowler.com/bliki/InversionOfControl.html)
