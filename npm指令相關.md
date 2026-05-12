
> -g 代表全域安裝，等同於安裝在電腦上

1. 確認套件是否安裝成功(以`redocly/cli`為例)：
```powershell
npm list -g @redocly/cli
```

2. 查看 npm 全域安裝路徑：
```powershell
npm config get prefix
```

3. 確認系統環境變數的`path`是否包含npm 全域安裝路徑：
```powershell
# 檢查 PATH 是否包含 npm 路徑 
$env:PATH -split ";" | Where-Object { $_ -like "*npm*" }
```

