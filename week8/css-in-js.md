## 학습 키워드

- CSS in JS 란
- CSS

## CSS-in-JS

```jsx
import styled from 'styled-components';
// Create a component that renders a <p> element with blue text
const BlueText = styled.p`
  color: blue;
`;

<BlueText>My blue text</BlueText>
```

자바스크립트를 사용하여 컴포넌트의 스타일을 지정하는 스타일링 기법이다. 

styled-components , JSS, Emotion 등의 라이브러리들이 있다.

### 왜 이 기술을 사용해야하는가?

> [React: CSS in JS (2014)](https://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.html)
> 
1. Global Namespace
2. Dependencies
3. Dead Code Elimination
4. Minification
5. Sharing Constants
6. Non-deterministic Resolution
7. Isolation

> [A Unified Styling Language (2017)](https://blog.rhostem.com/posts/2017-06-24-unified-styling-language)
> 
1. Scoped styles
2. Critical CSS
3. Smarter optimisations
4. Package management
5. Non-browser styling

> [All You Need To Know About CSS-in-JS (2017)](https://d0gf00t.tistory.com/22)
> 
1. Thinking in components
2. CSS-in-JS **leverages the full power of the JavaScript ecosystem** to enhance CSS.
3. True rules isolation
4. Scoped selectors
5. Vendor Prefixing
6. Code sharing
7. **Only the styles which are currently** in use on your screen are also in the DOM (react-jss).
8. Dead code elimination
9. Unit tests for CSS

## 성능 이슈

> [CSS-in-JS와 성능 (2021)](https://hyeonseok.com/blog/877)
> 

→ CSS 파일과 JS 파일 로딩의 차이.

- JS의 크기를 줄이는 것이 성능에 유리하다.
- css는 별도의 파일로 분리하는 것이 더 좋다.

> [Why We're Breaking Up with CSS-in-JS (2022)](https://bit.ly/3g6QufF)
> 

대안:

- [Linaria](https://linaria.dev/)
    - CSS를 평범한 텍스트로 작성.
    - React에 종속적이지 않지만, React Styled Component도 지원함.
- [vanilla-extract](https://vanilla-extract.style/)
    - CSS를 오브젝트 형태로 표현. React의 Inline Style과 유사함.
    - React와 무관하게 사용 가능.