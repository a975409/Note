
> 展開語法最常用在要「複製一個物件，並為該物件添加一些屬性時」

舉例來說，我們先定義智慧型手機的基本屬性：
```js
// 定義一個物件
const mobilePhone = {
  name: 'mobile phone',
  publishedYear: '2019',
};
```

接下來如果要複製 `mobilePhone` 這個物件變成一個新的物件（`iPhone`），同時添加一些屬性時，可以使用 `...` 展開語法：
```js
/* 展開語法（spread syntax） */

const iPhone = {
  ...mobilePhone,
  name: 'iPhone',
  os: 'iOS',
};

console.log(iPhone);  // { name: 'iPhone', publishedYear: '2019', os: 'iOS' }
```

展開語法同樣可以用來複製陣列，並在新的陣列中添加元素，像是這樣：
```js
/* 展開語法（spread syntax） */

const mobilesOnSale = ['Samsung', 'Apple', 'Huawei'];
const allMobiles = [...mobilesOnSale, 'Oppo', 'Vivo', 'Xiaomi'];

console.log(allMobiles); // [ 'Samsung', 'Apple', 'Huawei', 'Oppo', 'Vivo', 'Xiaomi' ]
```