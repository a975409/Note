
https://yucj.gitbooks.io/ecmascript-6/content/docs/module-loader.html

>瀏覽器對於帶有`type="module"`的`<script>`，都是非同步載入

載入方式如下，`type`需設為`module`：
```html
<script type="module" src="foo.js"></script>
```

`<script>`標籤的`async`屬性也可以開啟，這時只要載入完成，瀏覽器就會中斷渲染，並且會立即執行模組。執行完成後，瀏覽器再繼續渲染。
```html
<script type="module" src="foo.js" async></script>
```

ES6 模組也允許內嵌在網頁中，語法行為與載入外部指令碼完全一致。
```html
<script type="module">
  import utils from "./utils.js";

  // other code
</script>
```

對於上例外部的模組指令碼，有幾點需要注意。
- 程式碼是在模組作用域之中執行，而不是在全域性作用域執行。模組內部的頂層變數，外部不可見。
- 模組指令碼自動採用嚴格模式，不管有沒有宣告`use strict`。
- 模組之中，可以使用`import`命令載入其他模組（`.js`字尾不可省略，需要提供絕對 URL 或相對 URL），也可以使用`export`命令輸出對外介面。
- 模組之中，頂層的`this`關鍵字返回`undefined`，而不是指向`window`。也就是說，在模組頂層使用`this`關鍵字，是無意義的。
- 同一個模組如果載入多次，將只執行一次。