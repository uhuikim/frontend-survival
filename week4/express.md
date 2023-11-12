# express

## 학습 키워드

- Express 란
- URL 구조
- REST API
- HTTP Method(CRUD)

## Express

> [Express](https://expressjs.com/ko/)
> 

express는 node.js의 웹 프레임워크다. 프론트를 공부를 하고있지만 백엔드에 대한 이해가 필요하고 BFF등 여러가지 이유로 백엔드라 불리는 영역들을 건드리는 경우가 많다. Next.js사용할때도 api 를 건드리는데 백엔드에대한 이해없이 프론트만 작업하는건 불가능하다. 테스트에서도 모킹을 하는 작업을 해야하기 때문에 이해를 해두면 좋다.

### 간단한 서버 앱 npm 패키지 세팅

> [Express 설치](https://expressjs.com/ko/starter/installing.html)
> 

> [ts-node](https://github.com/TypeStrong/ts-node)
> 

```bash
mkdir express-demo-app
cd mkdir express-demo-app
```

잊지 말고 .gitignore 파일 준비!

```bash
touch .gitignore
echo "/node_modules/" > .gitignore
```

패키지 초기화

```bash
npm init -y
```

TypeScript

```bash
npm i -D typescript
npx tsc --init
```

ESLint

```bash
npm i -D eslint
npx eslint --init
```

- To check syntax, find problems, and enforce code style
- JavaScript modules (import/export)
- None of these
- Popular > xo

ts-node 설치

```bash
npm i -D ts-node
```

- 타입스크립트 사용을 위함

Express 설치

```bash
npm i express
npm i -D @types/express
```

package.json

```jsx
"scripts": {
    "start": "nodemon app.ts",
    "lint": "eslint --fix ."
},
```

### Hello World 예제

> [Express 예제](https://expressjs.com/ko/starter/hello-world.html)
> 

TypeScript에 맞춰서 app.ts 파일 작성.

```jsx
import express from 'express';

const port = 3000;

const app = express();

app.get('/', (req, res) => {
	res.send('Hello, world!');
});

app.listen(port, () => {
	console.log(`Server running at http://localhost:${port}`);
});
```

ts-node로 실행.

```bash
npx ts-node app.ts
```

코드를 수정할 때마다 서버를 재실행해야 하는 문제를 피하기 위해 [nodemon](https://github.com/remy/nodemon) 사용.

```bash
npm i -D nodemon
npx nodemon app.ts
```

## REST API

> Roy Fielding - “[Architectural Styles and the Design of Network-based Software Architectures](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)” (2000) - http 표준을 만들때 참여하신 분
> 

> [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html) - 성숙도 모델 , 하이퍼 미디어를 강조한다.
> 

대개는 “필딩 제약 조건” 4가지를 모두 만족하지 않고, Resource와 HTTP Verb만 도입하는 수준으로 사용.

- 이렇게 안 하고: /write-post
- 이렇게 하자: /posts → 뭔가를 한다 (CRUD)

CRUD에 대해 HTTP Method를 대입. Read는 Collection(복수)과 Item(Element)(단수)로 나뉨.

기본 리소스 URL: /products

1. Read (Collection) → GET /products ⇒ 상품 목록 확인
2. Read (Item) → GET /products/{id} ⇒ 특정 상품 정보 확인
3. Create (Collection Pattern 활용) → POST /products ⇒ 상품 추가 (JSON 정보 함께 전달)
4. Update (Item) → PUT 또는 PATCH /products/{id} ⇒ 특정 상품 정보 변경 (JSON 정보 함께 전달)
5. Delete (Item) → DELETE /products/{id} ⇒ 특정 상품 삭제

`put`은 없는 것은 만들어주고 아니면 전체를 덮어쓴다. 

`patch`는 일부만 변경을 해준다. patch를 많이 쓴다. 

put, patch, delete는 기본적인 브라우저에서는 처리할 수 없고, fetch api를 이용해서 ajax 요청등을 통해서 처리할 수 있다. 브라우저의 기본 동작은 아니다.

상품을 안보이게 하고 싶음 ⇒ delete사용 ⇒ delete를 했다고 해서 정말로 DB에서 지울 필요는 없다. 안보이게 한다는 의미로 사용 가능, patch를 이용해서 hidden을 y로 설정해도 된다.

**Thinking in React 예제**

```jsx
app.get('/products', (req, res) => {

const products = [
    {
        category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
    },
    {
        category: 'Fruits', price: '$1', stocked: true, name: 'Dragonfruit',
    },
    {
        category: 'Fruits', price: '$2', stocked: false, name: 'Passionfruit',
    },
    {
        category: 'Vegetables', price: '$2', stocked: true, name: 'Spinach',
    },
    {
        category: 'Vegetables', price: '$4', stocked: false, name: 'Pumpkin',
    },
    {
        category: 'Vegetables', price: '$1', stocked: true, name: 'Peas',
    },
];
	
res.send({ products }); // 객체를 쓰면 자동으로 JSON으로 바꿔준다.
// res.send( products); 이렇게 줘도 상관없지만 혹시라도 다른 것을 함께 보내 줄수도 있으니까!!
// res.send({products , pages : {currentPage : 1, totalPage : 10}});
});
```

### 터미널 창에서 보고 싶은 경우

- curl [localhost:3000/products](http://localhost:3000/products)
- http [localhost:3000/products](http://localhost:3000/products)