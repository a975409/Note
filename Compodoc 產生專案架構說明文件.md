### 1. 安裝 Compodoc

首先，你需要在專案中安裝 Compodoc。你可以使用 npm 或 yarn 來安裝：
```
npm install @compodoc/compodoc --save-dev
```

### 2. 設定 Compodoc
在專案根目錄下，建立一個 `compodoc.json` 檔案，用於設定 Compodoc 的生成選項。以下是一個基本的設定範例：
```JSON
{
  "name": "My Angular Project",
  "output": "docs",
  "tsconfig": "tsconfig.json",
  "includes": ["src/**/*.ts"],
  "theme": "gitbook",
  "hideGenerator": true,
  "disableSourceCode": false,
  "disableDomTree": false,
  "disableTemplateTab": false,
  "disableGraph": false,
  "disableCoverage": false,
  "disablePrivate": false,
  "disableProtected": false,
  "disableInternal": false,
  "disableLifeCycleHooks": false,
  "disableRoutesGraph": false,
  "disableSearch": false,
  "disableDependencies": false,
  "disableStyleTab": false,
  "disableExamples": false,
  "disableMainGraph": false
}
```

### 3. 執行 Compodoc

在vscode下執行此指令
```
npx compodoc -p tsconfig.json
```

### 4. 查看生成的文件

Compodoc 會將生成的文件存放在 `docs` 目錄下（根據 `compodoc.json` 中的 `output` 設定）。你可以打開 `index.html` 檔案來查看完整的專案架構說明文件。

### 5. 自訂文件內容

如果你需要進一步自訂文件的內容，可以在 `compodoc.json` 中調整相關設定，或者在專案中的 TypeScript 檔案中加入 JSDoc 註解來提供更多的資訊。

### 6. 自動化生成（選項）

如果你希望每次建置專案時都自動生成文件，可以在 `package.json` 中加入一個 script：
```json
"scripts": {   "docs": "compodoc -p tsconfig.json" }
```

> 這樣你就可以使用 `npm run docs` 或 `yarn docs` 來生成文件。

### 7. 啟動內建的網頁伺服器，預設連接埠為 8080（選項）
```
npx compodoc -s
```

