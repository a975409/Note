> 當執行function更新值後，會將當前組件重新渲染，重新渲染後才會更新值
> 當該state所屬的組件被停止(移除)渲染時，state也會跟著消失

[状态与渲染树中的位置相关](https://zh-hans.react.dev/learn/preserving-and-resetting-state#state-is-tied-to-a-position-in-the-tree)
[相同位置的相同组件会使得 state 被保留下来](https://zh-hans.react.dev/learn/preserving-and-resetting-state#same-component-at-the-same-position-preserves-state)
[在相同位置重置 state](https://zh-hans.react.dev/learn/preserving-and-resetting-state#resetting-state-at-the-same-position)

使用方式：
```jsx
//count：可讀取的常數(const)
//setCount：更新常數(const)的function，使用方式：setCount(1)
const [count, setCount] = useState(0);
```

[[useState 更新object]]
[[useState 更新陣列]]

## 每一個`useState`更新，都會納入一個隊列

[把一系列 state 更新加入队列](https://zh-hans.react.dev/learn/queueing-a-series-of-state-updates)

程式碼：
```jsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setNumber(n => n + 1);
        setNumber(42);
      }}>增加数字</button>
    </>
  )
}
```

輸出結果：
> 在下一次渲染期间，React 会遍历 state 队列：

| 更新队列         | `n`      | 返回值         |
| ------------ | -------- | ----------- |
| “替换为 `5`”    | `0`（未使用） | `5`         |
| `n => n + 1` | `5`      | `5 + 1 = 6` |
| “替换为 `42`”   | `6`（未使用） | `42`        |

> 然后 React 会保存 `42` 为最终结果并从 `useState` 中返回。

## 在下次渲染前多次累加同一个 state值：
正確寫法：
```jsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}

/* 輸出結果
number=3
*/
```

錯誤寫法：
[渲染会及时生成一张快照](https://zh-hans.react.dev/learn/state-as-a-snapshot#rendering-takes-a-snapshot-in-time "Link for 渲染会及时生成一张快照")

```jsx
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}

/* 輸出結果
number=1
*/
```
