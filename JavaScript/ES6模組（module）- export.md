> `export`命令用於規定模組的對外介面

新增`profile.js`建立模組：
1. 輸出`變數`：
``` javascript
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;

//另一種輸出變數的方式
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```

2. 輸出`函式`或`class`：
```javascript
export function multiply(x, y) {
  return x * y;
};
```

也可以使用`as`關鍵字針對輸出的`變數`、`函式`或`class`重新命名：
```javascript
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```

總結來說，正確的`export`輸出設定方式如下：
```javascript
// 寫法一
export var m = 1;

// 寫法二
var m = 1;
export {m};

// 寫法三
var n = 1;
export {n as m};
```

另外，`export`語句輸出的介面，與其對應的值是動態繫結關係，即通過該介面，可以取到模組內部實時的值。
以下是模組內的程式碼：
```javascript
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
```

> 最後，`export`命令可以出現在模組的任何位置，只要處於模組頂層就可以。

然後一定要定義`export default ....`，否則瀏覽器會載入錯誤