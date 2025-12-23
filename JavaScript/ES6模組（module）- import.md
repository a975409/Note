1. 使用`export`命令定義了模組的對外介面以後，其他 JS 檔案就可以通過`import`命令載入這個模組：
```javascript
// main.js
import {firstName, lastName, year} from './profile';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

2. 如果想為`import`的變數重新命名，`import`命令要使用`as`關鍵字，將輸入的變數重新命名。
```javascript
import { lastName as surname } from './profile';
```

3. `import`後面的`from`指定模組檔案的位置，可以是相對路徑，也可以是絕對路徑，`.js`路徑可以省略。如果只是模組名，不帶有路徑，那麼必須有配置檔案，告訴 JavaScript 引擎該模組的位置。
```javascript
import {myMethod} from 'util';
```

4. 注意，`import`命令具有提升效果，會提升到整個模組的頭部，首先執行：
以下範例執行不會出錯
``` javascript
foo();

import { foo } from 'my_module';
```

5. 假設要載入模組內所有指定為`export`的參數，則可使用`*`符號，並且要使用`as`關鍵字重新命名：
``` javascript
// circle.js模組
export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}

// main.js
//載入 circle.js模組
import * as circle from './circle';

console.log('圓面積：' + circle.area(4));
console.log('圓周長：' + circle.circumference(14));
```

6. 假設有一個`circleplus`模組，繼承了`circle`模組：
```javascript
// circleplus.js

//繼承circle模組
export * from 'circle';
export var e = 2.71828182846;
export default function(x) {
  return Math.exp(x);
}
```

繼承`circle`時也可只輸出`circle`模組的`area`方法，且將其改名為`circleArea`：
```javascript
// circleplus.js

export { area as circleArea } from 'circle';
```

7. 程式碼中的`import exp`表示，將`circleplus`模組的預設方法(`export default`)載入為`exp`方法：
```javascript
// main.js
import exp from 'circleplus';
```

8. 動態載入：透過外來變數或條件，決定要載入哪個模組：
```javascript
const main = document.querySelector('main');

import(`./section-modules/${someVariable}.js`)
  .then(module => {
    module.loadPageInto(main);
  })
  .catch(err => {
    main.textContent = err.message;
  });

```

> 執行階段才會執行，並且採用非同步載入


9. 如果想同時載入多個模組，可以採用下面的寫法：
```javascript
Promise.all([
  import('./module1.js'),
  import('./module2.js'),
  import('./module3.js'),
])
.then(([module1, module2, module3]) => {
   ···
});
```

10. `import()`也可以用在 async 函式之中。
```javascript
async function main() {
  const myModule = await import('./myModule.js');
  const {export1, export2} = await import('./myModule.js');
  const [module1, module2, module3] =
    await Promise.all([
      import('./module1.js'),
      import('./module2.js'),
      import('./module3.js'),
    ]);
}

main();
```