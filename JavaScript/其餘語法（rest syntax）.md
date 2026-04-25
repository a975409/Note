>其餘語法和展開語法的寫法一樣，都是 `...`，但是使用時機不太一樣，如果說展開語法像是「解壓縮」，那麼其餘語法就像是「壓縮」。它可以把在解構賦值中沒有被取出來的物件屬性或陣列元素都放到一個壓縮包裡。

舉例來說，在前面談到物件解構賦值的時候，從伺服器拿到一大包資料：
```js
const product = {
  name: 'iPhone',
  image: 'https://i.imgur.com/b3qRKiI.jpg',
  description:
    '全面創新的三相機系統，身懷萬千本領，卻簡練易用。電池續航力突飛猛進，前所未見。令你大為驚豔的晶片更加碼機器學習技術，並突破智慧型手機所能成就的極限。第一部威力強大，Pro 如其名的 iPhone，全新登場。',
  brand: {
    name: 'Apple',
  },
  aggregateRating: {
    ratingValue: '4.6',
    reviewCount: '120',
  },
  offers: {
    priceCurrency: 'TWD',
    price: '26,900',
  },
};
```

從`product`物件中取出`name`、`description`
而其他尚未被取出的屬性值全都歸屬在`...other`(可定義其他名稱，但前面要加上`...`)
```js
/* 物件解構賦值時使用其餘語法 */
const { name, description, ...other } = product;
console.log(other);

// {
//   image: 'https://i.imgur.com/b3qRKiI.jpg',
//   brand: { name: 'Apple' },
//   aggregateRating: { ratingValue: '4.6', reviewCount: '120' },
//   offers: { priceCurrency: 'TWD', price: '26,900' }
// }
```

透過其餘語法可以從陣列中，取出銷量前三名的手機，分別是：`best, second, third`
其餘未沒被取出來的陣列元素全都歸屬在`...other`(可定義其他名稱，但前面要加上`...`)
```js
/* 陣列解構賦值時使用其餘語法 */
const mobileBrands = [
  'Samsung', 'Apple', 'Huawei', 'Oppo',
  'Vivo', 'Xiaomi', 'LG', 'Lenovo', 'ZTE'
];

// 變數名稱不一定要取名為 other
const [best, second, third, ...other] = mobileBrands;
console.log(other);   // [ 'Oppo', 'Vivo', 'Xiaomi', 'LG', 'Lenovo', 'ZTE' ]
```