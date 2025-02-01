> 如果是windows環境，則需開啟 Windows PowerShell 執行指令

1. 切換到專案目錄內：
```
cd [完整專案路徑]
```

2. 下載並安裝 npm 套件，使用如下 npm CLI 指令：
```
npm install
```

3. 使用以下 CLI 指令在本地執行專案：
```
ng serve
```

> 執行上述指令後，如果要在瀏覽器中檢視你的應用，請訪問 http://localhost:4200/

如果預設連接埠 4200 不可用，則可以使用連接埠標誌指定另一個連接埠，如下所示：
```
ng serve --port 4201
```

> 當啟動了應用的開發伺服器時，你可以編輯程式碼並在瀏覽器中檢視對此更改的自動更新。要停止此 `ng serve` 指令，請按 `Ctrl` + `c` 鍵。

[在本地執行你的應用](https://angular.tw/start/start-deployment#running-your-application-locally)