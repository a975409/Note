> RxJS 可透過`scan`函數來建立專用的變數
```javascript
var button = document.querySelector('button'); 

//創建一個按鈕事件，在這個按鈕事件之外使用了全域變數(count)
var count = 0;
button.addEventListener('click', () => console.log(`Clicked ${++count} times`));

//使用 RxJS，在scan建立專用變數(count)
Rx.Observable.fromEvent(button, 'click') 
.scan(count => count + 1, 0) 
.subscribe(count => console.log(`Clicked ${count} times`));
```

> .scan用法請參考[[RxJS scan]]