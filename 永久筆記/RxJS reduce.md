
https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-reduce

> 用來取得最新一筆累加的值，但需要`take`搭配使用

angular專案引用方式如下：
```typescript
import { reduce, take } from 'rxjs';
```

程式碼如下：
```typescript
let addCarNoBtn = document.getElementById('addCarNoBtn');

if (addCarNoBtn) {
  fromEvent(addCarNoBtn, 'click')
	.pipe(
	  take(1),
	  reduce((carNoCount) => carNoCount + 1, this.currentInputCarNoCount)
	)
	.subscribe((carNoCount) => {
	  console.log('addCarNoBtn', carNoCount);
	  //this.currentInputCarNoCount = carNoCount;
	});
}
```

> `take(1)`代表只抓取最新一筆的值