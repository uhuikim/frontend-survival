# 학습 키워드

- Node.js
- NPM(Node Package Manager)
  - package.json / package-lock.json
  - node_modules
  - npx
- ES Modules vs CommonJS

# Javascript 개발 환경(Node.js) 세팅

- Node.js
- fnm(fast node manager) ⇒ node 버전을 관리하기 위한 툴

## fnm 설치하기

```jsx
brew install fnm
open ~/.zshrc
eval "$(fnm env --use-on-cd)" // 추가

// LTS 버전을 설치 및 default로 설정
fnm install --lts
fnm use lts-latest
fnm default $(fnm current)

// node 버전 확인하기 (현재 기준 18.18.2)
node -v

```

[fnm 설치방법](https://github.com/Schniz/fnm#zsh)

## TypeScript + React + Jest + ESLint + Parcel 개발 환경 세팅

1. 폴더를 하나 생성해서 vs code로 열어준다.
2. `npm` 패키지 준비하기

```bash
npm init -y // 모든 설정을 다 yes로 하겠다~
```

3. `gitignore` 생성하기

- [만들어주는 싸이트](https://www.toptal.com/developers/gitignore) or [github제공 ignore](https://github.com/github/gitignore/blob/main/Node.gitignore)

4. 타입스크립트 설정

```bash
npm i -D typescript

npx tsc --init
// ./node_modules/.bin/tsc 와 동일함
//  tsconfig.json을 생성한다.
```

- dependencies : 프로그램에서 쓸것
- devDependencies : 프로그램에서 쓰는건 아니고 개발 환경에서 씀
  ⇒ 두개를 분리 함으로써 배포등에서 용량을 줄일 수 있음

<blockquote>
💡 npx는 실행할때 사용한다.

- 로컬 -> 글로벌 -> npm(remote)
- 로컬에 있으면 로컬에 있는 것을 실행
- 글로벌하게 있으면 글로벌에 있는 것을 실행
- 내 컴퓨터(로컬 또는 글로벌)에 없다면 원격 npm에서 찾아서 실행 후 삭제
- npx create-react-app 의 그 npx!!
  </blockquote>

5. tsconfig.json 파일의 jsx속성 주석 풀어주기

```bash
"jsx": "react-jsx"
```

6. Eslint 설정

```bash
npm i -D eslint

npx eslint --init
// to check syntax, find problems,and enforce code style
// javascript modules (import/export)
// xo typescript (aribnb를 선호하지만 여기서는 xo사용)
```

- 조금더 수정을 하고 싶으면 [옛날 문서](https://github.com/ahastudio/CodingLife/blob/main/20211008/react/.eslintrc.js) 참고하기
- env에 jest:true 추가해주기

7. `.eslintignore` 파일 작성

- eslint실행할 때 이곳은 빼달라~

8. 리액트 설치

```bash
npm i react react-dom

npm i -D @types/react @types/react-dom
```

9. 테스트 도구 설치

```bash
npm i -D jest @types/jest @swc/core @swc/jest \
    jest-environment-jsdom \
    @testing-library/react @testing-library/jest-dom
```

기본으로 안쓰고 swc를 사용할꺼다

10. `jest.config.js` 작성

- [참고](https://github.com/ahastudio/CodingLife/blob/main/20220726/react/jest.config.js)

11. parcel 도구 설치

```bash
npm i -D parcel
```

12. `package.json` 파일 scripts 수정하기

```bash
"source" : "index.html",
"scripts": {
    "start": "parcel --port 8080",
    "build": "parcel build",
    "check": "tsc --noEmit",
    "lint": "eslint --fix --ext .js,.jsx,.ts,.tsx .",
    "test": "jest",
    "coverage": "jest --coverage --coverage-reporters html",
    "watch:test": "jest --watchAll"
  },
```

13. 기본 코드 작성(앞으로 만들게 예정)

- index.html
- src/main.tsx
- src/App.tsx
- src/App.test.tsx
- src/components/Greeting.test.tsx
- src/components/Greeting.tsx

## 실행 코드 정리

npx eslint . ⇒ eslint로 모든 파일을 검사한다.

npx eslint —fix . ⇒ eslint로 모든 파일 검사 및 수정을 해줌

npm start : source에 적혀있는 파일이 실행된다.

npm build : 빌드해서 dist파일이 생성됨
