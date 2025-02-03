#### 建立專案：
`Standalone API`（無 NgModule）：
```
ng new StandaloneAPI-app --ssr=false --style=css
```

`ng new`指令**明確指定了一些額外選項**：
🔹 `--ssr=false`  
👉 **關閉 SSR（伺服器端渲染）**，確保專案是純前端的 SPA（單頁應用程式）。

🔹 `--style=css`  
👉 **用於樣式檔案的副檔名/預處理器，設為css。

[ng new指令請參考](https://angular.tw/cli/new#options)

> Angular 19 如果沒特別設定的話，預設採用`Standalone API`架構開發，以及預設開啟路由功能。
   另外產生的元件(Component)並不會特別標上`standalone: true`，因為在`bootstrapApplication()`會將將所有元件都設為`standalone: true`

#### 專案架構
![[Pasted image 20250131165656.png]]

`/src` 目錄下有以下檔案：
1. `index.html` 是應用程式的頂級 HTML 樣板。
2. `style.css` 是應用程式的頂級樣式表。
3. `main.ts` 是應用程式開始執行的地方。
4. `favicon.ico` 是應用程式的圖示
5. `.angular` 包含建立 Angular 應用程式時所需的檔案。
6. `.e2e` 包含用於測試應用程式的檔案。
7. `.node_modules` 包含應用程式使用的 node.js 套件。
8. `angular.json` 將嚮應用程式建立工具描述 Angular 應用程式。
9. `package.json` 被 `npm`（Node 套件管理器）用來執行已完成的應用程式。
10. `tsconfig.*` 是向 TypeScript 編譯器描述應用程式設定的檔案。

`/src/app`目錄下有以下檔案：
1. `app.component.ts` 是描述 `app-root` 元件的源檔案。這是應用程式中的頂級 Angular 元件。
2. `app.component.css` 是這個元件的樣式表。
3. 往後建立的元件會新增到此目錄中。

`/src/assets`：存放應用程式會用到的靜態檔案。

#### 其他開發筆記
[[Angular 元件(Component)]]
[[Angular 設定路由導向]]
[[Angular 建立Form表單並取得使用者輸入]]
[[Angular 使用 HttpClient 發出http request]]
