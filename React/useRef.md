>與`useState`不同，`useRef`更新值不會觸發新的渲染

## 使用方式：
```jsx
export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    //更新變數
    ref.current = ref.current + 1;
    
    //讀取變數
    alert('你点击了 ' + ref.current + ' 次！');
  }

  return (
    <button onClick={handleClick}>
      点击我！
    </button>
  );
}
```

## 使用 ref 操作 DOM
```jsx
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    // 可以使用任意浏览器的 API，例如：
    inputRef.current.focus();
  }

  return (
    
    //DOM節點的ref，綁定指定的useRef
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        聚焦输入框
      </button>
    </>
  );
}
```
