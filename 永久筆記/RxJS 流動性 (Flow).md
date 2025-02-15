>RxJS 提供了一整套操作符來幫助你控制事件如何流經 observables 。
>流程控制操作符有 [**filter**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-filter)、[**delay**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-delay)、[**debounceTime**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-debounceTime)、[**take**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-take)、[**takeUntil**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-takeUntil)、[**distinct**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-distinct)、[**distinctUntilChanged**](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-distinctUntilChanged) 等等

下面的程式碼顯示的是如何`控制一秒鐘內最多點擊一次`：
```javascript
var button = document.querySelector('button');

//一般寫法
var count = 0;
var rate = 1000;
var lastClick = Date.now() - rate;
button.addEventListener('click', () => {
  //控制一秒鐘內最多點擊一次
  if (Date.now() - lastClick >= rate) {
    console.log(`Clicked ${++count} times`);
    lastClick = Date.now();
  }
});

//使用 RxJS
Rx.Observable.fromEvent(button, 'click')
  .throttleTime(1000) //控制一秒鐘內最多點擊一次
  .scan(count => count + 1, 0)
  .subscribe(count => console.log(`Clicked ${count} times`));
```

> .scan用法請參考[[RxJS scan]]