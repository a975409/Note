[官方教學](https://cn.rx.js.org/manual/overview.html#h37)
https://jonny-huang.github.io/angular/training/26_rxjs/
https://jonny-huang.github.io/angular/training/32_rxjs_6/
https://fullstackladder.dev/blog/2020/09/27/mastering-rxjs-12-observable-subject-behaviorsubject-replaysubject-asyncsubject/
https://ithelp.ithome.com.tw/users/20103367/ironman/1199

> RxJS 是一個函式庫，它透過使用 observable 序列來編寫非同步和基於事件的程式。它提供了一個核心類型 Observable，附屬類型 (Observer、 Schedulers、 Subjects) 和受 [Array#extras] 啟發的操作子 (map、filter、reduce、every, 等等)，這些陣列運算子可以把非同步事件當作集合來處理。

>ReactiveX 結合了 觀察者模式、迭代器模式 和 使用集合的函數式編程，以滿足以一種理想方式來管理事件序列所需的一切。
#### 入門範例：
```javascript
var button = document.querySelector('button'); 

//註冊事件監聽器的常規寫​​法
button.addEventListener('click', () => console.log('Clicked!'));

//使用 RxJS
Rx.Observable.fromEvent(button, 'click')
.subscribe(() => console.log('Clicked!'));
```

> Rx.Observable.fromEvent=>建立HTML事件

[[RxJS 純淨性 (Purity)]]
[[RxJS 流動性 (Flow)]]
[[RxJS 值 (Values)]]

[[RxJS scan]]
[[RxJS reduce]]
