[[展開語法（spread syntax）]]
[[其餘語法（rest syntax）]]

1. 複製陣列：
```javascript
const squares = Array(9).fill(null);
const nextSquares = squares.slice();
nextSquares[i] = "X";
```

 2. 建立指定陣列元素數量的空陣列：
 ```javascript
 const squares = Array(9).fill(null);
 ```

3. 透過 Array.from 一次產生帶有 n 個元素的陣列
在建立帶有多個元素的陣列時，經常會使用到 [`Array.from()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 這個方法，下面列出常用的方式：
```js
// 產生元素數目為 10，元素值都為 undefined 的陣列
Array.from({ length: 10 }); // [undefined, undefined, ..., undefined]

// 產生元素數目為 10，元素值為 0 ~ 9 的陣列
Array.from({ length: 10 }, (_, index) => index); // [0, 1, 2, ..., 8, 9]
```

因此透過 `Array.from()` 我們可以先建立一個帶有 n 個元素的陣列，並取名為 `counters`：

```js
// STEP 1: 建立元素數目為 14，內容為 [0, 1, ..., 13]
const counters = Array.from({ length: 14 }, (_, index) => index);
```

> `Array.from()` 詳細的用法可以參考 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 的說明