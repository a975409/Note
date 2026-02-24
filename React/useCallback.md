
> `useCallback` 是一个允许你在多次渲染中缓存函数的 React Hook。

https://zh-hans.react.dev/reference/react/useCallback

```jsx
const cachedFn = useCallback(fn, dependencies)

//範例
import { useCallback } from 'react';
export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
```

#### 参数

- `fn`：想要缓存的函数。此函数可以接受任何参数并且返回任何值。在初次渲染时，React 将把函数返回给你（而不是调用它！）。React 不会调用此函数，而是返回此函数。你可以自己决定何时调用以及是否调用。
    
- `dependencies`：原則上所有在 `fn` 函数中所使用的變數，但不一定都要全部設定，以陣列呈現。

#### 返回值

在初次渲染时，`useCallback` 返回你已经传入的 `fn` 函数

在之后的渲染中, 如果 `dependencies` 没有改变，`useCallback` 返回上一次渲染中缓存的 `fn` 函数；否则返回这一次渲染传入的 `fn`。