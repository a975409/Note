>對於流經 observables 的值，你可以對其進行轉換。

下面的程式碼展示的是如何`累積每次點擊的滑鼠 x 座標`
```javascript
var button = document.querySelector('button');

//一般寫法
var count = 0;
var rate = 1000;
var lastClick = Date.now() - rate;
button.addEventListener('click', (event) => {
  //控制一秒鐘內最多點擊一次
  if (Date.now() - lastClick >= rate) {
    count += event.clientX;
    console.log(count)
    lastClick = Date.now();
  }
});

//使用 RxJS
Rx.Observable.fromEvent(button, 'click')
  .throttleTime(1000) //控制一秒鐘內最多點擊一次
  .map(event => event.clientX)
  .scan((count, clientX) => count + clientX, 0) //累積每次點擊的滑鼠 x 座標
  .subscribe(count => console.log(count));
```

> .scan用法請參考[[RxJS scan]]