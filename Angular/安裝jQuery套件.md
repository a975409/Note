
https://www.npmjs.com/package/jquery

執行以下指令：
```
npm i jquery
```

安裝typescrpit描述檔：
```
npm install --save-dev @types/jQuery
```

在`angular.json`設定jQuery套件的路徑：
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
            "styles": [],
            "scripts": [
              "./node_modules/jquery/dist/jquery.min.js",
            ]
          },
        },
```

> 通常在開發的時候，執行的環境是`build`，所以只要設定`build`就好

承上點，在`tsConfig`指定的檔名內的`types`陣列內新增`jQuery`
```json
/* To learn more about Typescript configuration file: https://www.typescriptlang.org/docs/handbook/tsconfig-json.html. */
/* To learn more about Angular compiler options: https://angular.dev/reference/configs/angular-compiler-options. */
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": ["jquery"]
  },
  "files": [
    "src/main.ts"
  ],
  "include": [
    "src/**/*.d.ts"
  ]
}

```

如果要在component 使用jQuery指令，則需新增如下：
```typescript
import { Component } from '@angular/core';

declare var $: any; // 引入 jQuery

@Component({
  selector: 'app-date-picker',
  template: `<input type="text" class="form-control" id="datepicker">`
})
export class DatePickerComponent {
  
}

```