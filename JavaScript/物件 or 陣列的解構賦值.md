#### 物件的解構賦值

>常見的情況時，當從伺服器拿到的資料是**帶有一大包內容的物件，而我們只需要用到該物件裡面的其中一些屬性**，這時就很適合使用解構賦值。

舉例來說，現在從伺服器拿到一大包和商品名稱有關的資料：
```javascript
// 一個帶有非常多資料的物件
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

在使用物件的解構賦值之後，可以達到**快速建立變數並取值的動作**：
```javascript
/* 物件的解構賦值 */

// 自動產生名為 name 和 description 的變數
// 並把 product 物件內的 name 和 description 當作變數的值
const { name, description } = product;

console.log(name);         // iPhone
console.log(description);  // 全面創新的三相機系統，身懷萬千本領，卻簡練易用。...
```

#### 進階：透過解構賦值取出物件中的物件
可取得 `product.offers.price`這個變數，但無法存取`offers` 這個變數：
```javascript
const {
  offers: { price },
} = product;

console.log(price);    // 26,900
console.log(offers);   // ReferenceError: offers is not defined
```

#### 陣列的解構賦值
```javascript
const mobileBrands = [
  'Samsung', 'Apple', 'Huawei', 'Oppo',
  'Vivo', 'Xiaomi', 'LG', 'Lenovo', 'ZTE'
];

// 取得 mobileBrands 陣列中的第一、第二和第三個元素當作變數的值帶入 
const [best, second, third] = mobileBrands; 
console.log(best); // Samsung 
console.log(second); // Apple
console.log(third); // Huawei
```