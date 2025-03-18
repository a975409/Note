
https://www.npmjs.com/package/bootstrap

執行以下指令：
```
npm i sweetalert2
```

在`angular.json`設定sweetalert2套件的路徑：
```json
"build": {
          "builder": "@angular-devkit/build-angular:application",
          "options": {
            "outputPath": "dist/system-wanhai-frontend",
            "index": "src/index.html",
            "browser": "src/main.ts",
            "polyfills": ["zone.js"],
            "tsConfig": "tsconfig.app.json",
            "assets": ["src/assets"],
            "styles": ["./node_modules/sweetalert2/dist/sweetalert2.css",],
            "scripts": [
             "./node_modules/sweetalert2/dist/sweetalert2.min.js",
            ]
          },
        },
```

> 通常在開發的時候，執行的環境是`build`，所以只要設定`build`就好

在`compnent`使用時，需`import`如下
```typescript
import Swal from 'sweetalert2';
```

>這樣就能使用`Swal`指令了