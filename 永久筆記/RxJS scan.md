https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-scan

angular專案引用方式如下：
```typescript
import { scan } from 'rxjs';
```
#### scan參數：
![[Pasted image 20250215192825.png]]

| 參數名稱        | 參數型態                                         | 參數說明               |
| ----------- | -------------------------------------------- | ------------------ |
| accumulator | function(acc: R, value: T, index: number): R | 對每個來源資料呼叫的累加器函數    |
| seed        | T \| R                                       | 可選，設定`R`的初始值       |
| scan回傳值     | `Observable<R>`                              | 帶有累加功能的observable。 |
>`R`=>來源參數
>`T`=>每次`R`固定累加的值

#### 範例說明：
每點擊一次按鈕，就會累積每次點擊的滑鼠 x 座標
```javascript
var button = document.querySelector('button'); 
Rx.Observable.fromEvent(button, 'click')
  .map(event => event.clientX)
  .scan((count, clientX) => count + clientX, 0) //累積每次點擊的滑鼠 x 座標
  .subscribe(count => console.log(count));
```

`.scan((count, clientX) => count + clientX, 0)`說明如下：
> `accumulator`=>`(count, clientX) => count + clientX`
>  - acc=>count，累加後的來源值
>  - value=>clientX，是從map接收的
>  - 回傳累加後的`R`值，也就是`count`，並會帶入下一次的`R`做累加
> 
> `seed`=>0，設定`R`的初始值，也就是`count`

執行`scan`後產生結果如下，代表`scan`會逐項列出每筆累加後的值：
```
11
10
9
8
7
6
5
4
3
2
```