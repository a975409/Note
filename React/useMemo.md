
>`useMemo` 是一个 React Hook，它在每次重新渲染的时候能够缓存计算的结果。

https://zh-hans.react.dev/reference/react/useMemo#skipping-re-rendering-of-components

```jsx
const cachedValue = useMemo(calculateValue, dependencies)

// 範例
import { useMemo } from 'react';

function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
}
```

#### 参数
- `calculateValue`：要缓存计算值的函数。它应该是一个没有任何参数的纯函数，并且可以返回任意类型。

- `dependencies`：所有在 `calculateValue` 函数中所使用的變數，以陣列呈現。

#### 返回值

在初次渲染时，`useMemo` 返回不带参数调用 `calculateValue` 的结果。

在接下来的渲染中，如果 `dependencies` 没有发生改变，它将返回上次缓存的值；否则将再次调用 `calculateValue`，并返回最新结果

