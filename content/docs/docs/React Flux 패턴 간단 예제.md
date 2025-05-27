```ts
<script lang="ts">
	import Button from '$lib/components/ui/Button.svelte';
	import { createStore } from '$lib/flux/store';

	const store = createStore<number>(0, (state: number, action: { type: string }): number => {
		switch (action.type) {
			case 'INCREMENT':
				return state + 1;
			case 'DECREMENT':
				return state - 1;
			default:
				return state;
		}
	});
</script>

<div>
	<Button onclick={() => store.dispatch({ type: 'DECREMENT' })}>-</Button>
	<span>{store.getState()}</span>
	<Button onclick={() => store.dispatch({ type: 'INCREMENT' })}>+</Button>
</div>
```

```ts
export type Action = {
	type: string;
};

export class Dispatcher<T> {
	private listeners: Array<(action: T) => void> = [];

	register(listener: (action: T) => void): () => void {
		this.listeners.push(listener);
		return () => {
			this.listeners = this.listeners.filter((l) => l !== listener);
		};
	}

	dispatch(action: T): void {
		this.listeners.forEach((listener) => listener(action));
	}
}
```

```ts
import { Dispatcher, type Action } from '$lib/flux/dispatcher';

type Reducer<T> = (state: T, action: Action) => T;

export class Store<T> {
	private state: T;
	private dispatcher: Dispatcher<Action> = new Dispatcher();
	private reducer: Reducer<T>;

	constructor(state: T, reducer: Reducer<T>) {
		this.state = state;
		this.reducer = reducer;
	}

	getState(): T {
		return this.state;
	}

	dispatch(action: Action): void {
		console.log('state', this.state);
		this.state = this.reducer(this.state, action);
		this.dispatcher.dispatch(action);
	}

	subscribe(listener: () => void): () => void {
		const unregister = this.dispatcher.register(() => listener());
		return () => {
			unregister();
		};
	}
}

export const createStore = <T>(state: T, reducer: Reducer<T>): Store<T> => {
	return new Store(state, reducer);
};
```

작동은 되지 않는다. 왜냐하면 Svelte 환경은 React와 다르기 때문이다.