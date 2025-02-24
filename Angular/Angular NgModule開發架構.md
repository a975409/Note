#### 建立專案：
`NgModule`：
```
ng new NgModule-app --no-standalone --ssr=false --style=css
```

`ng new`指令**明確指定了一些額外選項**：

🔹 `--no-standalone`  
👉 **不使用 Standalone API**，改為傳統的 `NgModule` 模式。

🔹 `--ssr=false`  
👉 **關閉 SSR（伺服器端渲染）**，確保專案是純前端的 SPA（單頁應用程式）。

🔹 `--style=css`  
👉 **用於樣式檔案的副檔名/預處理器，設為css。

[ng new指令請參考](https://angular.tw/cli/new#options)

[[Angular 元件(Component)(NgModule開發架構)]]
[[Angular 設定路由導向(NgModule開發架構)]]
[[Angular 使用 HttpClient 發出http request(NgModule開發架構)]]