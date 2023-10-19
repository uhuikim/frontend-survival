## 학습 키워드

- Bundler(번들러)
  - Parcel
- Lint(린트)
  - ESLint

# Parcel

🚀 [**Parcel 공식문서**](https://parceljs.org/)

번들러 : 여러개의 자바스크립트 파일을 하나로 합쳐줌

옛날에는 브라우저에서 import, export를 직접 지원을 하지 않았다.

합치는 김에 최신버전의 문법을 예전 버전으로 변환 등의 작업도 해줌

- 특별한 설정 없이 바로 사용 가능한 빌드 도구. 내부적으로 SWC를 사용해 기존 도구보다 빠르다(ES Module을 적극 활용하는 Vite도 엄청나게 빠름).
- 참고:
  - https://github.com/ahastudio/til/tree/main/parcel
  - https://github.com/ahastudio/til/tree/main/vite

설정이 필요 없다고 했지만, 다음 두가지 작업은 하는 게 좋다.

`package.json` 파일에 source 속성 추가.

```json
"source": "./index.html",
```

[parcel-reporter-static-files-copy](https://github.com/elwin013/parcel-reporter-static-files-copy) 패키지 설치 후 `.parcelrc` 파일 작성.  
이렇게 하면 static 폴더의 파일을 정적 파일로 Serving할 수 있다(이미지 등 Assets).

```json
{
  "extends": ["@parcel/config-default"],
  "reporters": ["...", "parcel-reporter-static-files-copy"]
}
```

빌드 + 정적 서버 실행

```bash
npx parcel build

npx servor ./dist
```

- [servor](https://github.com/lukejacksonn/servor)

# ESLint

💡 [**ESLint 공식문서**](https://eslint.org/)

- [린트](<https://ko.wikipedia.org/wiki/린트_(소프트웨어)>)
- [정적 프로그램 분석](https://ko.wikipedia.org/wiki/정적_프로그램_분석)
- [Coding_conventions](https://en.wikipedia.org/wiki/Coding_conventions)

무엇을 위해서?

- 스타일 통일
- 잠재적 문제 발견
- 베스트 프랙티스 추천 → 최신 트렌드를 학습하는데 활용할 수 있다.
- 일관성이 있는 코드! 한사람이 짠거 같은 코드를 만들어준다.

[VS Code ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

⇒ 추가적인 설정을 해 줄 수 있다.

`.vscode/settings.json`파일을 추가해 JS/TS 파일을 저장할 때마다 ESLint를 실행하고 문제점을 고치게 설정할 수 있다.

```json
{
  "editor.rulers": [80],
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "trailing-spaces.trimOnSave": true
}
```

- 타입스크립트 npm run check를 하면 컴파일 하면서 타입 체크를 한다.
- 개발하면서 npm run lint && npm run check 을 많이 하게 된다.
