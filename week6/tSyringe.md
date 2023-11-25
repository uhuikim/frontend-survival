## 학습 키워드

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- singleton (싱글톤)

## TSyringe

> [TSyringe](https://github.com/microsoft/tsyringe)
> 

> [reflect-metadata](https://github.com/rbuckton/reflect-metadata)
> 

> [The problem with passing props](https://beta.reactjs.org/learn/passing-data-deeply-with-context#the-problem-with-passing-props)
> 

TypeScript용 DI 도구(IoC Container). External Store를 관리하는데 활용할 수 있다. React 컴포넌트 입장에서는 “전역”처럼 여겨진다. “Prop Drilling” 문제를 우아하게 해결할 수 있는 방법 중 하나(React로 한정하면 Context도 쓸 수 있다).

내가 뭔가를 써야하는데 전역변수 등의 방법으로 얻지 않고 제대로된 방법으로 얻어서 쓰고 싶다. 테스트, 미묘하게 컴포넌트(프로그램을 구성하는 요소)를 조립해야하는 상황에서 많이 사용한다.

의존성 설치

```bash
npm i tsyringe reflect-metadata
```

`src/main.tsx` 파일과 `src/setupTests.ts` 파일에서 reflect-metadata 임포트.

```tsx
import 'reflect-metadata';
```

@(decorator)를 사용해주기 위해 tsconfig에서 주석을 풀어준다

```jsx
"experimentalDecorators": true,
 "emitDecoratorMetadata": true,
```

싱글톤으로 관리할 CounterStore 클래스를 준비:

```tsx
import { singleton } from 'tsyringe';

@singleton()
class CounterStore {
	// …(중략)...
}
```

싱글톤 CounterStore 객체를 사용:

```tsx
import { container } from 'tsyringe';

const counterStore = container.resolve(CounterStore);
```

테스트에서 TSyringe에서 관리하는 객체를 초기화할 수 있다.

```tsx
container.clearInstances();
```

## 간단하게 external store 만들기

```jsx
// stores/CounterStore.ts
import { singleton } from 'tsyringe';

type Listener = () => void;

@singleton()
export default class CounterStore {
  count = 0;

  listeners = new Set<Listener>();

  publish() {
    this.listeners.forEach((listener) => {
      listener();
    });
  }

  addListener(listener : Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener : Listener) {
    this.listeners.delete(listener);
  }
}
```

```jsx
// hooks/useCounterStore.ts

import { useEffect } from 'react';
import { container } from 'tsyringe';
import CounterStore from '../stores/CounterStore';
import useForceUpdate from './useForceUpdate';

export default function useCounterStore() {
  const store = container.resolve(CounterStore);
  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);
    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  return store;
}
```

```jsx
import useCounterStore from '../hooks/useCounterStore';

export default function Counter() {
  const store = useCounterStore();

  return (
    <div>
      <p>
        Count :
        {store.count}
      </p>
    </div>
  );
}
```

```jsx
import { container } from 'tsyringe';
import CounterStore from '../stores/CounterStore';

export default function CounterControl() {
  const store = container.resolve(CounterStore);

  const handleClickIncrease = () => {
    store.count += 1;
    store.publish();
  };

  const handleClickDecrease = () => {
    store.count -= 1;
    store.publish();
  };

  return (
    <div>
      <p>{store.count}</p>
      <button type="button" onClick={handleClickIncrease}>
        Increase
      </button>
      <button type="button" onClick={handleClickDecrease}>
        Decrese
      </button>
    </div>
  );
}
```

- 리렌더링을 하고 싶은경우 값을 사용하는 곳에서 forceUpdate를 등록을 해줘야한다.
- Counter 2개를 렌더링을 할 경우 아래에서 forceUpdate를 덮어쓰기 때문에 아래 컴포넌트에서만 count값 변경을 확인할 수 있다. set을 이용해서 여러개의 forceUpdate를 관리해준다.
- forceUpdate를 해주는 로직을 Hook으로 분리해서 사용하면 편리하다.
- 캡슐화가 필요할 경우 store에 작성을 해준다.
    
    ```jsx
    const handleClickIncrease = () => {
        store.count += 1;
        store.publish();
      };
    
      const handleClickDecrease = () => {
        store.count -= 1;
        store.publish();
      };
    
    // 캡슐화하기
    
    const handleClickIncrease = () => {
        store.increase()
      };
    
      const handleClickDecrease = () => {
        store.decrease()
      };
    
    ```
    

# 테스트 코드 작성하기

```jsx
import { fireEvent, render, screen } from '@testing-library/react';
import { container } from 'tsyringe';
import App from './App';

const context = describe;

test('App', () => {
  render(<App />);
});

describe('App', () => {
// 테스트 코드에서는 각각 독립적이어야한다. 전역처럼 쓰고 있어서 독립적이지 않다.
// 초기화를 해줘야한다. 
beforeEach(() => {
    container.clearInstances();
  });

  context('when press increase button once', () => {
    test('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));
      expect(screen.getAllByText('Count : 1')).toHaveLength(2);
    });
  });

  context('when press increase button twice', () => {
    test('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));
      fireEvent.click(screen.getByText('Increase'));
      expect(screen.getAllByText('Count : 2')).toHaveLength(2);
    });
  });
});
```