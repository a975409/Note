
https://www.npmjs.com/package/bootstrap

執行以下指令：
```
npm i bootstrap
```

安裝typescrpit描述檔：
```
npm install --save-dev @types/bootstrap
```

在`angular.json`設定bootstrap套件的路徑：
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
            "styles": ["./node_modules/bootstrap/dist/css/bootstrap.min.css",],
            "scripts": [
             "./node_modules/bootstrap/dist/js/bootstrap.bundle.min.js",
            ]
          },
        },
```

> 通常在開發的時候，執行的環境是`build`，所以只要設定`build`就好

承上點，在`tsConfig`指定的檔名內的`types`陣列內新增`bootstrap`
```json
/* To learn more about Typescript configuration file: https://www.typescriptlang.org/docs/handbook/tsconfig-json.html. */
/* To learn more about Angular compiler options: https://angular.dev/reference/configs/angular-compiler-options. */
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "outDir": "./out-tsc/app",
    "types": ["bootstrap"]
  },
  "files": [
    "src/main.ts"
  ],
  "include": [
    "src/**/*.d.ts"
  ]
}

```