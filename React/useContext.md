### Step 1：创建 context

LevelContext.js
```jsx
import { createContext } from 'react';

//Context名稱開頭一定要大寫
export const LevelContext = createContext(1);
```

### Step 2：使用 Context 讀取值

Heading.js
```jsx
import { LevelContext } from './LevelContext.js';
import { useContext } from 'react';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  // ...
}
```

### Step 3：提供 context
```jsx
import { LevelContext } from './LevelContext.js';

//children=Heading.js
export default function Section({ level, children }) {
  return (
    <section className="section">
      <LevelContext value={level}>
        {children}
      </LevelContext>
    </section>
  );
}
```
>这告诉 React：“如果在 `<Section>` 组件中的任何子组件请求 `LevelContext`，给他们这个 `level`。”组件会使用 UI 树中在它上层最近的那个 `<LevelContext>` 传递过来的值。

也可以這樣使用：
```jsx
import { LevelContext } from './LevelContext.js';
import { useContext } from 'react';

//children=Heading.js
export default function Section({ children }) {
  //透過context取得level值
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext value={level}>
        {children}
      </LevelContext>
    </section>
  );
}
```