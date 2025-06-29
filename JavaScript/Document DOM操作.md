
範例如下：
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <p id="title"></p>
    <button id="btn">按我</button>
    <div id="box"></div>
    <input type="text" id="input1" />
    <script src="./../index.js"></script>
  </body>
</html>
```

```javascript
window.addEventListener("load", function () {
  const p1 = document.getElementById("title");

  //<p id="title"></p>內新增文字
  p1.innerText = "訂閱布魯斯前端";

  const b1 = document.getElementById("btn");

  //按鈕點擊事件
  b1.addEventListener("click", function () {
    console.log("點下去");
  });

  const box1 = document.getElementById("box");
  
  //<div id="box"></div>內新增HTML語法
  box1.innerHTML = "<p>Test</p>";

  const in1 = document.getElementById("input1");

  //輸入按鍵的事件
  in1.addEventListener("keyup", function (e) {
    console.log("e.target.value", e.target.value);
  });
});
```

以上述範例所示，解說程式碼
1. 取得DOM物件：
```javascript
  //取得id=title的HTML tag
  const in1 = document.getElementById("input1");
```

2. 建立監聽事件：
[可用事件參考](https://developer.mozilla.org/zh-CN/docs/Web/Events)
```javascript
in1.addEventListener("keyup", function (e) {
    console.log("e.target.value", e.target.value);
  });
```

> function(e)的e參數等同於事件本身`Event`，`e.target`代表最初觸發事件的 DOM 物件。

[Event](https://developer.mozilla.org/zh-TW/docs/Web/API/Event#%E6%96%B9%E6%B3%95)
[Event.preventDefault() 取消事件的預設行為，但不阻止事件傳遞](https://developer.mozilla.org/zh-TW/docs/Web/API/Event/preventDefault)
[Event.stopPropagation() 阻止事件物件繼續捕捉或冒泡傳遞](https://developer.mozilla.org/zh-TW/docs/Web/API/Event/stopPropagation)
