# Redux 따라하기

## 학습 키워드

- Redux
- Reflect

- 공용으로 사용되는 BaseStore를 만들어준다.
- store에서 값을 가져올때 사용하는 useSelector
    - 기존 store의 값이 변경되면 forceUpdate를 시켜주는 로직을 관리하는 useStore를 useSelector안에 넣어줬다.
    - 단일 스토어이기 때문에 count랑 상관없는 애들도 foreceUpdate시 useSelector를 사용한 곳 모두 리렌더링이 된다.이에 대한 처리를 추가해줘야한다.
- action을 넘겨줄때 사용하는 dispatch 관련 로직을 빼놓은 useDispatch

`src/stores/BaseStore.ts` 파일

```tsx
export type Action<Payload> = {
	type: string;
	payload?: Payload;
}

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Reducers<State> = Record<string, Reducer<State, any>>;

type Listener = () => void;

export default class BaseStore<State> {
	state: State;

	reducer: Reducer<State, any>;

	listeners = new Set<Listener>();
	
	constructor(initialState: State, reducers: Reducers<State>) {
		this.state = initialState;

		this.reducer = (state: State, action: Action<any>): State => {
			const reducer = Reflect.get(reducers, action.type);			
			if (!reducer) {
				return state;
			}
			return reducer(state, action);
		};
	}
	
	dispatch<T>(action: Action<T>) {
		this.state = this.reducer(this.state, action);
		this.listeners.forEach((listener) => listener());
	}
	
	addListener(listener: Listener) {
		this.listeners.add(listener);
	}
	
	removeListener(listener: Listener) {
		this.listeners.delete(listener);
	}
}
```

`src/stores/Store.ts` 파일

```tsx
import { singleton } from 'tsyringe';

import BaseStore, { Action } from './BaseStore';

const initialState = {
	count: 0,
	name: 'Tester',
};

export type State = typeof initialState;

function increase(state: State, action: Action<number>) {
	return {
		...state,
		count: state.count + (action.payload ?? 1),
	};
}

function decrease(state: State) {
	return {
		...state,
		count: state.count - 1,
	};
}

const reducers = {
	increase,
	decrease,
};

@singleton()
export default class Store extends BaseStore<State> {
	constructor() {
		super(initialState, reducers);
	}
}
```

`src/hooks/useDispatch.ts` 파일

```tsx
import { container } from 'tsyringe';

import { Action } from '../stores/BaseStore';

import Store from '../stores/Store';

export default function useDispatch<Payload>() {
	const store = container.resolve(Store);
	return (action: Action<Payload>) => store.dispatch(action);
}
```

`src/hooks/useSelector.ts` 파일

```tsx
import { container } from 'tsyringe';

import { useEffect, useState } from 'react';

import Store, { State } from '../stores/Store';

import useForceUpdate from './useForceUpdate';

type Selector<T> = (state: State) => T;

export default function useSelector<T>(selector: Selector<T>): T {
	const store = container.resolve(Store);
	
	const [state, setState] = useState<T>(selector(store.state));
	
	const forceUpdate = useForceUpdate();
	
	useEffect(() => {
		const update = () => {
			const newState = selector(store.state);
			// TODO: T가 object일 때 처리 필요함.
			if (newState !== state) {
				setState(newState);
				forceUpdate();
			}
		};
	
		store.addListener(update);
		return () => store.removeListener(update);
	}, [store, forceUpdate]);

	return state;
}
```

Dispatch와 Selector 사용.

```tsx
const dispatch = useDispatch();
const count = useSelector((state) => state.count);

dispatch({ type: 'increase' });
dispatch({ type: 'decrease' });

dispatch({ type: 'increase', payload: 10 });
```